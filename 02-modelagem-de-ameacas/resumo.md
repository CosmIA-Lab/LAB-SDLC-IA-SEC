# Ponto 2 — Modelagem de ameaças e análise de superfície de ataque · resumo para releitura

**Em uma frase.** Responder com método, na fase de design, "o que pode dar errado?": desenhar o sistema com fronteiras de confiança, enumerar ameaças por elemento com STRIDE, dar destino a cada uma e provar que o destino foi cumprido (Shostack, 2014) — e, antes, decidir por onde a ameaça entra, medindo e reduzindo a superfície de ataque (Manadhata e Wing, 2011).

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

## O que é e por quê
Ameaça é o que pode acontecer; vulnerabilidade é por onde; risco é probabilidade × impacto (NIST SP 800-30). A modelagem trabalha na ameaça, antes de haver código e bug. Revisão sem método acha o que o revisor já conhece; "pense como atacante" é conselho, não método (Shostack, cap. 1). A estrutura por elemento torna a cobertura independente de quem revisa.

## Fundamentos
- **Toda ameaça viola uma propriedade**: 6 propriedades (confidencialidade, integridade, disponibilidade, autenticação, autorização, não repúdio) → 6 ameaças → lista finita (Shostack, Tab. 3-1).
- **Ameaça não se elimina, decide-se**: toda ameaça encontrada precisa de destino; ignorada ≠ aceita.
- **Ameaça concentra-se na fronteira de confiança**: nível de confiança = quem controla, com que privilégio, o que já foi verificado (Swiderski e Snyder, 2004); a fronteira é onde isso muda e onde identidade e integridade se reverificam.
- **O atacante escolhe a porta**: o defensor precisa de todas; o tamanho da superfície é o tamanho da defesa.

## Mapa
MERMAID

## As quatro perguntas — etapa, técnica, saída
| Etapa | Pergunta | Princípios | Técnica (fonte) | Mecanismo | Saída |
|---|---|---|---|---|---|
| 1 | O que estamos construindo? | fronteira explícita; nível certo de detalhe; sistema real | DFD (DeMarco, 1979): entidade externa, processo, fluxo, armazenamento + fronteira de confiança (Shostack, cap. 2) | Threat Dragon, Microsoft TMT, pytm | DFD com fronteiras |
| 2 | O que pode dar errado? | estrutura, não intuição; cobertura por elemento; ameaça ligada ao desenho | STRIDE (Kohnfelder e Garg, 1999); STRIDE por elemento (Shostack, cap. 3): entidade externa S R · processo STRIDE · fluxo T I D · armazenamento T I D (+R se log); attack tree (Schneier, 1999) | geração por elemento; jogo EoP (Shostack, 2010) | lista de ameaças com elemento e categoria |
| 3 | O que fazemos a respeito? | nenhuma sem decisão; mitigação é requisito; risco ordena; aceitar exige dono | 4 destinos: mitigar, eliminar, transferir, aceitar; mitigação por categoria (cap. 8): S→authn, T→integridade, R→log/assinatura, I→cripto/authz, D→limites, E→authz; risco = prob × impacto (SP 800-30); bug bar | registro com destino; backlog | requisito com critério de aceite |
| 4 | Fizemos um bom trabalho? | cobertura verificável; mitigação prova-se no teste; modelo acompanha desenho; do time | checklist: diagrama, cobertura, destino, teste; teste como prova (SP 800-115) | modelo versionado; revisão no pipeline | rastreabilidade ameaça → requisito → controle → teste |

## STRIDE × propriedade
| Letra | Ameaça | Propriedade negada | Mitigação típica |
|---|---|---|---|
| S | spoofing | autenticação | MFA, assinatura, certificado |
| T | tampering | integridade | hash, MAC, TLS, parametrização |
| R | repudiation | não repúdio | log íntegro com timestamp, assinatura |
| I | information disclosure | confidencialidade | criptografia, autorização, minimização |
| D | denial of service | disponibilidade | rate limit, cota, redundância |
| E | elevation of privilege | autorização | checar permissão por objeto, menor privilégio |

## Superfície de ataque
Definição: métodos (entradas e saídas), canais (portas, sockets) e itens de dado (parâmetros) por onde se entra ou extrai dado (Manadhata e Wing, 2011). Princípios: redução antes de proteção; exposição define prioridade (anônimo > autenticado > interno); padrão fechado; inclui o que você não escreveu. Técnicas: métrica de superfície (2011), RASQ (Howard, Pincus e Wing, 2003), redução (Howard, 2003; MS SDL). Mecanismos: inventário por OpenAPI, nmap, SCA, feature flag desligada, API gateway. Saída: inventário por exposição e lista do que foi removido; o que ficou entra no DFD.

## Outras abordagens
PASTA (UcedaVélez e Morana, 2015): processo em 7 estágios, do negócio ao risco — processo, não taxonomia. LINDDUN (Deng et al., 2011): taxonomia de privacidade no mesmo DFD. NIST SP 800-154: centrada em dados. Threat Modeling Manifesto (2020): quatro perguntas, contínuo, do time. CAPEC, ATT&CK, OWASP Top 10: catálogos para a etapa 2, não métodos. DREAD (Howard e LeBlanc, 2003): abandonado pela Microsoft por inconsistência.

## O registro de ameaças
Colunas: id · elemento do DFD · STRIDE · descrição (agente, ação, alvo) · propriedade · ativo · destino (+ dono se aceitar) · requisito · controle · teste · estado. Exemplo: `A-07 | fluxo app→API | E | troca de id lê conta alheia | authz | saldo | mitigar | verificar dono em toda leitura | middleware | T-21 | verificada`. Vive com o código e muda quando o DFD muda.

## Em escala
Modelo do sistema uma vez; modelo pequeno por história (30–60 min) quando há gatilho: fronteira, entidade externa, classe de dado ou authn/authz nova. Do time, com segurança facilitando. Como código (pytm, Threagile) no pull request. Medir: % de funcionalidades com modelo, ameaças por idade, % com teste, escapes em produção. As falhas são organizacionais (Shostack, cap. 17–19): tarde, grande, sem dono, documento em vez de decisão.

## No exemplo da API de pagamentos
- DFD: app e parceiro (entidades externas), gateway/API/transferências (processos), banco/fila/log (armazenamentos), fronteiras app→API e API→parceiro.
- Ameaças: E na API (troca de id), T no fluxo API→banco (injeção), S no fluxo app→API (CSRF), I no canal do parceiro, D na consulta de saldo, R no log sem integridade.
- Destinos: middleware de autorização; parametrização; token anti-CSRF; TLS + assinatura (parte transferida por contrato); rate limit; log só-anexa.
- Prova: "conta de outro → 404"; webhook novo reabre a etapa 1.
- Superfície: 2 endpoints públicos, canal do parceiro, SDK; exportação sem cliente não entra.

## Erros a não cometer
- STRIDE é taxonomia, não método: sem DFD não há onde aplicá-lo.
- Modelagem não é pentest: antecipa no design; a intrusão verifica depois.
- Ameaça ≠ vulnerabilidade ≠ risco.
- DREAD não prioriza; risco = probabilidade × impacto e bug bar decidem.
- Aceitar não é ignorar: tem dono e prazo.
- Modela-se a cada mudança de fronteira, não uma vez.

## Três referências para citar
- Shostack (2014), *Threat Modeling: Designing for Security* — quatro perguntas, STRIDE por elemento, quatro destinos.
- Kohnfelder e Garg (1999) — STRIDE; Schneier (1999) — attack trees.
- Manadhata e Wing (2011) — métrica de superfície de ataque; Threat Modeling Manifesto (2020).

**Ponte.** A ameaça com destino "mitigar" vira requisito de arquitetura (ponto 3) e teste (ponto 7).
