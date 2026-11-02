# Árvore de memória — Ponto 9

Reconstrução de cabeça da segurança de APIs, microsserviços e contêineres.
Cada nó puxa o próximo; as **juntas** (⟪ ⟫) são a frase-ponte. Decore a
espinha; os ramos (P = princípios, T = técnicas, M = mecanismos) penduram nela.

Espinha mínima:
`o sistema virou muitos → 4 fundamentos → 3 anéis (borda · malha · pod) → APIs (Top 10 2023) → microsserviços → contêineres e orquestração → exemplo → erros`

```
                        ┌──────────────────────────────┐
                        │ APIs · MICROSSERVIÇOS · CONTÊINERES │
                        └──────────────┬───────────────┘
                                       ▼
              O QUE MUDOU QUANDO O SISTEMA VIROU MUITOS
              a fronteira se multiplicou (toda chamada entre serviços é fronteira) · o contrato é a superfície
              o contêiner é PROCESSO, não máquina · a plataforma é CÓDIGO
              nenhum princípio novo; todas as superfícies
                                       │
           ⟪ junta: antes das três partes, o que elas pressupõem ⟫
                                       ▼
                    FUNDAMENTOS (4)
        TODA CHAMADA CRUZA FRONTEIRA     BeyondProd 19 · NIST 800-204 (Chandramouli 19) → identidade de carga · mTLS · authz por chamada
        O CONTRATO É A SUPERFÍCIE        OpenAPI = inventário → o que não está não responde · esquema · authz por objeto E propriedade
        CONTÊINER = ISOLAMENTO PARCIAL   namespaces · cgroups · capabilities (14 por padrão) · seccomp (~44 bloqueadas) · núcleo compartilhado
                                         NIST 800-190 (17): imagem · registro · orquestrador · contêiner · nó
        PLATAFORMA É CÓDIGO              manifesto é superfície e é testável → Pod Security Standards · admissão · varredura
                                       │
           ⟪ junta: três anéis ⟫
                                       ▼
        clientes →[TLS · authn · rate limit · esquema]→ GATEWAY (PEP de borda) →[mTLS · identidade · authz]→ MALHA → serviços
        cada serviço em POD (sem root · sem capabilities · rootfs ro · seccomp · limites) · NetworkPolicy · egress restrito
        POLÍTICA COMO CÓDIGO atravessa os três · IDENTIDADE DE CARGA (SPIFFE) alimenta a malha
                                       │
                                       ▼
                    APIs — OWASP API Security Top 10 (2023)
        1 BOLA (objeto) · 2 authn · 3 propriedade (mass assignment + exposição excessiva) · 4 consumo irrestrito · 5 BFLA (função)
        6 fluxos de negócio · 7 SSRF · 8 config · 9 INVENTÁRIO · 10 consumo inseguro de APIs   → 6 de 10 são fronteira e contrato
        P: contrato é a verdade · validar na borda, AUTORIZAR NO SERVIÇO · entrada e saída por lista de permissão · todo recurso tem limite
           · resposta de terceiro é hostil · versão tem fim
        T/M: GATEWAY (Envoy, Kong) = Single Access Point: TLS · token (iss aud exp escopo) · rate limit · esquema · só rotas do catálogo · NÃO faz objeto
             esquema OpenAPI/JSON Schema → 400 na borda · GraphQL: profundidade · custo · introspecção off · persisted queries
             limites: token bucket · cota · tamanho · paginação · timeout · serializador por operação (3 campos, não o modelo)
             webhooks: HMAC do corpo · timestamp · id de evento · idempotência · catálogo gerado do implantado · descontinuação com prazo
        credencial por cliente: usuário → OAuth código+PKCE · parceiro → client credentials + mTLS (RFC 8705) · interno → SVID · chave de API só residual
        escopos por operação e recurso · GraphQL authz por CAMPO (@auth)
        incidentes: Capital One 19 (SSRF + IMDSv1 + IAM excessivo) · Tesla 18 (painel k8s sem authn) · Optus 22 / Peloton 21 (endpoint sem authn/BOLA + inventário)
                                       │
           ⟪ junta: dentro do cluster, cada conversa é uma fronteira ⟫
                                       ▼
                    MICROSSERVIÇOS — NIST 800-204 · Newman 21: complexidade operacional
        P: identidade de carga, não de rede · authn mútua em toda chamada · authz por chamada por política (800-204B) · segredo por identidade curto
           · rede nega por padrão · dado tem dono (menor mecanismo comum) · falha contida (timeout, circuit breaker)
        T/M: SPIFFE/SPIRE → SVID (X.509/JWT curto, rotacionado) · MALHA (Istio/Linkerd): proxy ao lado → mTLS automático · AuthorizationPolicy · telemetria (800-204A)
             sujeito original: token exchange (RFC 8693) ou afirmação assinada · ✗ X-User-Id em claro = deputado confuso
             segredos: Vault/KMS por identidade, credencial DINÂMICA de 1 h · ✗ env/ConfigMap · Secret só com etcd cifrado + RBAC + volume
             NetworkPolicy deny-all + regras · egress por proxy · fila: esquema (Avro) + ACL por tópico + idempotência · rastreamento com id de correlação
        SERVERLESS: privilégio por função · evento é entrada hostil · dependências pesam · segredo por identidade · efêmero ≠ seguro (OWASP Serverless Top 10 17)
        MULTI-TENANCY: tenant do token · filtro na camada de dados · RLS · cache prefixado · namespace/node pool/gVisor quando o tenant executa código · tenant no log
                                       │
           ⟪ junta: o contêiner é a última fronteira ⟫
                                       ▼
                    CONTÊINERES E ORQUESTRAÇÃO — NIST 800-190 · NSA/CISA Hardening Guide v1.2 (22) · CIS
        P: processo, não máquina · SEM ROOT · capabilities mínimas (drop ALL) · rootfs só-leitura · seccomp/AppArmor · nada do nó (hostPath, docker.sock, hostNetwork) · limites
        T/M: POD SECURITY STANDARDS: privileged · baseline · RESTRICTED (runAsNonRoot, allowPrivilegeEscalation false, drop ALL, seccomp RuntimeDefault) · PSA enforce/audit/warn (1.25)
             ADMISSÃO (Kyverno/Gatekeeper): registro aprovado · digest · assinatura + proveniência (ponto 8) · limites · NetworkPolicy obrigatória
             RBAC: cluster-admin só pessoas c/ MFA · service accounts mínimas · automount false · tokens vinculados (1.22) · ✗ SA que cria pods = escalada
             plano de controle: API server privado · etcd TLS + cifrado · kubelet autenticado · nó mínimo (Bottlerocket) · kube-bench
             runtimes isolados: gVisor 18 · Kata · Firecracker 20 (multi-tenant, código de cliente)
             detecção: FALCO (Sysdig 16) — shell em prod, escrita em /etc, conexão inesperada → ponto 10
             AUDIT LOG do API server: metadados de tudo, corpo do sensível · para fora do cluster · alertas (ClusterRoleBinding admin, exec, segredo)
        5 áreas NSA/CISA: pods · rede · authn/authz · registro e detecção · atualização e verificação
        ESCAPE concreto: CVE-2019-5736 runc — root dentro sobrescreve o binário no nó → sem root + no-new-privileges + seccomp + atualizar
                                       │
           ⟪ junta: o mesmo sistema, em produção ⟫
                                       ▼
                    EXEMPLO — API de pagamentos no cluster
        borda: Envoy TLS 1.2+ · token completo · 100/min consultas, 10/min transferências · OpenAPI do código · rotas do catálogo · WAF atrás · GraphQL prof. 6 + custo + persisted
        authz: gateway = escopo · serviço = objeto (middleware + Cedar) · resposta de 3 campos · comando de 3 campos
        serviços: Istio STRICT · SPIFFE por SA · AuthorizationPolicy (notificações → só GET contato) · token exchange · Vault dinâmico 1 h · NetworkPolicy deny · egress só webhooks · Avro + ACL
        webhooks: HMAC-SHA256 · 5 min · dedup · esquema
        pods: distroless por digest assinado · PSA restricted enforce · decodificador em C no gVisor · automount false · SA sem criar pods
        cluster: Kyverno (assinatura + SLSA L3 + limites + NetworkPolicy) · API privado · etcd cifrado · Bottlerocket semanal · kube-bench · cluster-admin = 3 pessoas FIDO2 · Falco → ponto 10
        → notificações comprometido: só GET contato · não lê segredo · não escapa · não cria pod · não sai · Falco vê o shell
                                       │
                                       ▼
                    ERROS A NÃO COMETER
        contêiner ≠ VM · mTLS ≠ authz · gateway ≠ proteção total (BOLA passa) · rede interna ≠ confiável · GraphQL 1 endpoint ≠ menos superfície
        imagem limpa ≠ pod seguro · segredo em env · RBAC do k8s ≠ authz da app · micro ≠ mais seguro por ser pequeno
```

Molde repetido de cada anel (decore uma vez, aplique três):
**o que mudou → princípios → técnica com fonte → mecanismo → o que NÃO faz**.
