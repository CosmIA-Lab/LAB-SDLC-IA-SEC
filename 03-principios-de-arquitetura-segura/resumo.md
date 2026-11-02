# Ponto 3 — Princípios de arquitetura e projeto seguros · resumo para releitura

**Em uma frase.** Regras sobre a forma do sistema que decidem, antes do código, como ele resiste quando algo falha: os oito princípios de Saltzer e Schroeder (1975) e suas releituras contemporâneas — defesa em profundidade, assume breach, zero trust, seguro por padrão — aplicadas por técnicas nomeadas (monitor de referência, padrões de segurança, compartimentação, arquitetura zero trust, revisão contra os 10 flaws).

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
Falha de design é erro de intenção: o código faz o que o projeto mandou, e o projeto estava errado (McGraw, 2006: metade dos defeitos). Corrige-se redesenhando, e redesenho depois do código custa o que Boehm mediu. Princípio vence checklist: a lista enumera falhas conhecidas; o princípio vale para a falha que ainda não aconteceu. Taxonomia: fundamento (premissa) → princípio (regra) → técnica (método com fonte) → mecanismo (implementação). RBAC é mecanismo; menor privilégio é princípio.

## Fundamentos
- **Confiança é decisão explícita, não estado padrão** (Kindervag, 2010: "centro mole"; NIST SP 800-207).
- **A violação vai acontecer em algum lugar** (assume breach): desenhar para limitar o raio de explosão.
- **Segurança depende de mecanismo pequeno e sempre invocado** (Anderson, 1972: monitor de referência — sempre invocado, inviolável, verificável; TCB mínima).
- **Quem opera o sistema é parte dele** (Adams e Sasse, 1999): controle inusável é controle desligado.

## Mapa
MERMAID

## Os oito princípios (Saltzer e Schroeder, 1975)
| Princípio | Regra | O que viola | Técnica / mecanismo |
|---|---|---|---|
| Economia de mecanismo | pequeno e inspecionável | authz espalhada; cripto própria | TCB mínima; middleware único; biblioteca padrão |
| Padrões à prova de falha | permissão, não exclusão; erro nega | lista de bloqueio; exceção que deixa passar | default deny; flag desligada; IAM vazio; *fail secure* (≠ fail-safe físico) |
| Mediação completa | todo acesso a todo objeto verificado | checar só na listagem; cache de authz; TOCTOU | monitor de referência; PDP/PEP; reavaliar por requisição |
| Desenho aberto | não depende do segredo do desenho (Kerckhoffs, 1883) | cripto própria; endpoint escondido | primitivas públicas; cofre para a chave |
| Separação de privilégios | duas chaves > uma | uma credencial faz tudo | MFA; separação de deveres (Clark e Wilson, 1987); revisor obrigatório |
| Menor privilégio | mínimo necessário | serviço como root; token com todos os escopos | privsep do OpenSSH (Provos et al., 2003); IAM por função; seccomp |
| Menor mecanismo comum | pouco compartilhado entre sujeitos | cache sem chave de tenant | isolamento por tenant; RLS; sandbox (Spectre como aviso) |
| Aceitabilidade psicológica | uso correto é o fácil | troca mensal de senha → post-it | SSO, passkeys; NIST SP 800-63B |
| + fator de trabalho; registro de comprometimento | custo de burlar; log íntegro quando não dá para impedir | — | bcrypt/Argon2, rate limit; trilha só-anexa (o R do STRIDE) |

## Releituras contemporâneas — e a linhagem
| Releitura | Regra | Combina | Fonte |
|---|---|---|---|
| Defesa em profundidade | camadas independentes em mecanismo e em falha | violação acontece + menor privilégio + mediação | NSA IATF (2000); Viega e McGraw (2001) |
| Assume breach | "até onde chega daqui?" | violação acontece + menor privilégio + registro | Microsoft (2010s) |
| Zero trust | acesso por identidade e sessão, não por rede; PDP/PEP; 7 tenets | confiança explícita + mediação + menor privilégio | Kindervag (2010); BeyondCorp (2014); NIST SP 800-207 (2020) |
| Seguro por padrão / design | fábrica segura; decidido no projeto | padrões à prova de falha + aceitabilidade | Howard e Lipner (2006) SD3+C; CISA (2023) |
| Separação de deveres | quem executa não aprova | separação de privilégios | Clark e Wilson (1987) |
| Elo mais fraco / não confiar no cliente | o atacante escolhe o mais fraco | confiança explícita + mediação | Viega e McGraw (2001); IEEE CSD #1 (2014) |

## Técnicas
| Técnica | Fonte | O que faz | Mecanismo |
|---|---|---|---|
| Monitor de referência / Check Point | Anderson (1972); Yoder e Barcalow (1997) | um ponto por onde todo acesso passa | middleware de authz; gateway; OPA/Cedar; policy as code |
| Padrões de segurança | Yoder e Barcalow (1997); Schumacher et al. (2006) | Single Access Point, Check Point, Roles, Session, Limited View, Full View with Errors, Secure Access Layer | gateway, RBAC, serializador por papel |
| Separação de privilégios / compartimentação | Provos, Friedl e Honeyman (2003) | código exposto roda sem privilégio; dano contido | contêiner sem root, seccomp, conta por ambiente |
| Arquitetura zero trust | NIST SP 800-207 | policy engine + administrator (PDP) e PEP no caminho; 3 variantes | mTLS, proxy ciente de identidade, tokens curtos, SPIFFE |
| Revisão contra os 10 flaws | IEEE CSD (2014) | confiança nunca presumida; authn inviolável; authz após authn; dado ≠ instrução; validação explícita; cripto correta; dado sensível; usuários; terceiros; mudança | revisão antes do código; ADR por item |
| Requisitos verificáveis | OWASP ASVS V1; NIST SP 800-160 | princípio vira requisito com nível e teste | backlog com critério de aceite; teste de arquitetura |

## No exemplo da API de pagamentos
- Troca de id → mediação completa → Check Point → middleware único → 403 em todo endpoint.
- Dependência vulnerável → menor privilégio → compartimentação → credencial só-leitura → lê, não move.
- Credencial interna roubada → confiança explícita → zero trust → mTLS + PDP/PEP + token curto.
- Operador interno → separação de deveres → 2º fator + 2º operador + log assinado.
- Canal do parceiro → desenho aberto + profundidade → TLS + assinatura + minimizar; falha nega.

## Erros a não cometer
- RBAC é mecanismo, não princípio.
- Zero trust é arquitetura (SP 800-207), não produto.
- Dois antivírus não são defesa em profundidade: camadas precisam ser independentes.
- Fail-safe (física: destranca) ≠ fail secure (software: nega).
- Desenho aberto ≠ publicar tudo; é não depender do segredo.
- SAST acha bug, não flaw: flaw exige revisão de arquitetura.

## Três referências para citar
- Saltzer e Schroeder (1975), *The Protection of Information in Computer Systems* — os oito princípios.
- Anderson (1972) — monitor de referência; IEEE CSD (2014) — os dez flaws.
- NIST SP 800-207 (2020) — Zero Trust; CISA (2023) — Secure by Design.

**Ponte.** A falha de design mal resolvida é o que o atacante explora como vulnerabilidade web (ponto 4).
