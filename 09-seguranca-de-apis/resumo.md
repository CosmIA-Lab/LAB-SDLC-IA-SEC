# Ponto 9 — Segurança de APIs, microsserviços e contêineres · resumo para releitura

**Em uma frase.** Quando o sistema vira muitos, nenhum princípio muda e todas as superfícies se multiplicam: cada chamada entre serviços é fronteira (identidade de carga, mTLS, autorização por chamada), cada contrato de API é superfície enumerável (gateway valida token, esquema e taxa; o serviço autoriza por objeto), e o contêiner é um processo com isolamento parcial (sem root, sem capabilities, rootfs só-leitura, seccomp) — tudo imposto por política como código.

## Roteiro de 10 páginas à mão (≈ 70 min)
| Pág. | Bloco | De onde vem neste resumo | Min |
|---|---|---|---|
| 1 | Introdução: definição, por quê, lugar no ciclo, anúncio das seções | "Em uma frase" + "O que é e por quê" | 6 |
| 2 | Fundamentos: as 4 premissas, cada uma com a consequência | "Fundamentos" | 7 |
| 3 | O mapa desenhado à mão e sua leitura (Figura 1) | "Mapa" | 6 |
| 4–5 | Princípios, cada linha da tabela vira um parágrafo com fonte | tabela de princípios | 14 |
| 6–7 | Técnicas: o que é, quem e quando, o que vê e o que não vê | tabela de técnicas | 14 |
| 8 | Mecanismos e um trecho de código ou configuração, com a regra que aplica | mecanismos + antes/depois | 7 |
| 9 | Exemplo guiado (API de pagamentos) e um caso real em uma frase | "No exemplo" + incidente citado | 8 |
| 10 | Erros a não cometer, tabela-síntese e ponte para o ponto vizinho | "Erros" + "Três referências" + "Ponte" | 8 |

Se o tempo apertar, corte nesta ordem: a página 8 (mecanismos) → a página 3 (mapa vira parágrafo) → metade das páginas 6–7. Nunca corte a 1, a 2 e a 10.

## Fundamentos
- **Toda chamada entre serviços cruza uma fronteira** (BeyondProd, 2019; NIST SP 800-204): a rede do cluster é o novo centro mole.
- **O contrato é a superfície**: o que não está no OpenAPI não responde; o que está tem esquema e política por objeto e propriedade.
- **O contêiner é processo com isolamento parcial** (NIST SP 800-190): namespaces, cgroups, 14 capabilities por padrão, seccomp (~44 bloqueadas), núcleo compartilhado; runc CVE-2019-5736 = escape.
- **A plataforma é código**: manifesto é superfície e é testável (Pod Security Standards, admissão, varredura).

## Mapa
MERMAID

## Três anéis
Gateway (borda): TLS, token (iss, aud, exp, escopo), rate limit, esquema, só rotas do catálogo — não autoriza por objeto. Malha (entre serviços): mTLS com identidade de carga, política de autorização por chamada, telemetria — não autoriza por objeto. Pod (processo): sem root, drop ALL, rootfs ro, seccomp, limites. Política como código atravessa os três.

## APIs — OWASP API Security Top 10 (2023)
| # | Categoria | Controle |
|---|---|---|
| 1 | BOLA (objeto) | autorização por objeto no serviço; filtro por dono na consulta |
| 2 | Autenticação quebrada | ponto 6; validação completa do token no gateway e no serviço |
| 3 | Propriedade (mass assignment, exposição excessiva) | objeto de comando e de resposta por operação, listas de permissão |
| 4 | Consumo irrestrito | rate limit, cota, tamanho, paginação, timeout; GraphQL: profundidade, custo, introspecção off, persisted queries |
| 5 | BFLA (função) | política por função; rota sem política não existe |
| 6 | Fluxos de negócio | limites por fluxo, detecção de automação, step-up |
| 7 | SSRF | lista de destinos pós-DNS, egress restrito |
| 8 | Configuração | ponto 4; varredura |
| 9 | Inventário | catálogo gerado do implantado; descontinuação com prazo; gateway recusa o não declarado |
| 10 | Consumo de APIs | resposta de terceiro é entrada hostil: esquema, timeout, sem redirect |

Credencial por cliente: usuário → OAuth código + PKCE; parceiro → client credentials + mTLS (RFC 8705); interno → SVID; chave de API só residual. Webhooks: HMAC do corpo, timestamp, id de evento, idempotência. Incidentes: Capital One 2019 (SSRF + IMDSv1 + IAM excessivo); Tesla 2018 (painel do Kubernetes sem autenticação); Optus 2022 e Peloton 2021 (endpoint sem autenticação ou BOLA + inventário).

## Microsserviços — NIST SP 800-204 / 204A / 204B
| Princípio | Técnica / mecanismo |
|---|---|
| Identidade de carga, não de rede | SPIFFE/SPIRE: SVID X.509 ou JWT curto, rotacionado, atestado por nó, namespace, service account |
| Autenticação mútua em toda chamada | malha (Istio, Linkerd): proxy ao lado, mTLS automático |
| Autorização por chamada, por política | AuthorizationPolicy: "notificações → contas só em GET contato" (800-204B: ABAC na malha) |
| Sujeito original propagado | token exchange (RFC 8693) ou afirmação assinada; nunca `X-User-Id` em claro (deputado confuso) |
| Segredo por identidade, curto | Vault/KMS; credencial dinâmica de 1 h por pod; nunca env/ConfigMap; Secret só com etcd cifrado + RBAC + volume |
| Rede nega por padrão | NetworkPolicy deny-all + regras; egress por proxy |
| Dado tem dono | cada serviço é dono do seu banco; fila com esquema e ACL por tópico |
| Falha contida | timeout, circuit breaker, bulkhead |

Serverless: privilégio por função; o evento é entrada hostil; dependências pesam; efêmero ≠ seguro. Multi-tenancy: tenant vem do token; filtro na camada de dados; RLS; cache prefixado; namespace, node pool e gVisor quando o tenant executa código; tenant no log.

## Contêineres e orquestração — NIST SP 800-190, NSA/CISA (2022), CIS
| Princípio | Mecanismo |
|---|---|
| Processo, não máquina | imagem mínima (distroless), sem shell nem compilador |
| Sem root; sem escalada | runAsNonRoot; allowPrivilegeEscalation: false |
| Capacidades mínimas | drop ALL; nunca privileged |
| Rootfs só-leitura; seccomp | readOnlyRootFilesystem; seccompProfile RuntimeDefault; AppArmor/SELinux |
| Nada do nó | sem hostPath, docker.sock, hostNetwork, hostPID |
| Limites | CPU e memória |

Pod Security Standards: privileged, baseline, restricted; Pod Security Admission (v1.25) por namespace em enforce/audit/warn. Admissão por política (Kyverno, Gatekeeper): registro aprovado, digest, assinatura + proveniência (ponto 8), limites, NetworkPolicy obrigatória. RBAC: cluster-admin só pessoas com MFA; service accounts mínimas; automount false; tokens vinculados (v1.22); SA que cria pods é escalada. Plano de controle: API server privado; etcd TLS e cifrado; kubelet autenticado; nó mínimo (Bottlerocket); kube-bench. Runtimes isolados: gVisor (2018), Kata, Firecracker (2020) para multi-tenant. Detecção: Falco (2016) — shell em produção, escrita em /etc, conexão inesperada. Audit log do API server para fora do cluster, com alertas. Cinco áreas NSA/CISA: pods, rede, authn/authz, registro e detecção, atualização e verificação.

## No exemplo da API de pagamentos
- Borda: Envoy TLS 1.2+; token completo; 100/min consultas, 10/min transferências; OpenAPI gerado do código; só catálogo; GraphQL com profundidade 6 e custo.
- Authz: gateway decide escopo; serviço decide objeto (middleware + Cedar); resposta de 3 campos.
- Serviços: Istio STRICT; SPIFFE por SA; AuthorizationPolicy mínima; token exchange; Vault dinâmico 1 h; NetworkPolicy deny; egress só webhooks.
- Pods: distroless por digest assinado; PSA restricted; decodificador em C no gVisor; automount false.
- Cluster: Kyverno exige assinatura + SLSA L3; API privado; etcd cifrado; kube-bench; 3 admins FIDO2; Falco → ponto 10.
- Resultado: serviço de notificações comprometido só alcança `GET contato`, não lê segredo, não escapa, não cria pod, não sai; o Falco vê o shell.

## Erros a não cometer
- Contêiner não isola como VM.
- mTLS prova quem chama; não autoriza.
- Gateway não protege a API inteira: BOLA passa.
- Rede interna do cluster não é confiável.
- Imagem sem CVE ≠ pod seguro (root, capabilities, hostPath).
- RBAC do Kubernetes não é a autorização da aplicação.

## Três referências para citar
- OWASP API Security Top 10 (2023).
- NIST SP 800-204 (2019), 800-204A (2020), 800-204B (2021); NIST SP 800-190 (2017).
- NSA e CISA, *Kubernetes Hardening Guide* v1.2 (2022); Pod Security Standards; BeyondProd (Google, 2019).

**Ponte.** A vulnerabilidade exposta em produção entra no funil de gestão de vulnerabilidades (ponto 10).
