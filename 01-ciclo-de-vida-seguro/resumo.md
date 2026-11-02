# Ponto 1 — Ciclo de vida de desenvolvimento seguro e DevSecOps · resumo para releitura

**Em uma frase.** Inserir atividades de segurança em cada fase do desenvolvimento, com verificação entre fases, porque o custo de corrigir cresce por ordem de grandeza a cada fase em que o defeito sobrevive (Boehm, 1981) — e, em DevSecOps, fazer isso dentro do fluxo contínuo, automatizado e compartilhado.

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
Segurança tratada como fase final chega tarde: a falha de design encontrada em produção exige redesenho; a mesma falha no quadro branco custa uma conversa. *Pushing left* (Janca, 2020) é a estratégia de mover a atividade de segurança para o mais cedo possível no ciclo. O ciclo de vida seguro é a moldura dos outros nove pontos: a modelagem de ameaças é a atividade da fase de requisitos e design; arquitetura, codificação, autenticação são a fase de código; os testes de segurança, a fase de testes; cadeia de suprimentos, a entrega; APIs e contêineres, a operação; gestão de vulnerabilidades, a manutenção que devolve o incidente como requisito do próximo ciclo.

## Fundamentos
- **O custo cresce com a fase** (Boehm, 1981): decidir antes é mais barato que remendar depois.
- **Falha de design ≠ bug de implementação** (McGraw, 2006): metade dos defeitos é de desenho e nenhuma ferramenta de código os acha.
- **Processo escrito ≠ processo executado**: só o que está no pipeline e é medido acontece sempre.
- **Segurança é responsabilidade de quem constrói**: o time central dá padrão, ferramenta e revisão; o time de produto é dono do resultado.

## Mapa
MERMAID

## As cinco fases — atividade e saída
| Fase | Atividade de segurança | Técnica (fonte) | Saída / gate |
|---|---|---|---|
| Requisitos | ativos, classificação, requisitos negativos, casos de abuso | abuse cases (McDermott e Fox, 1999); bug bar (SDL) | requisito verificável com teste associado |
| Design | modelagem de ameaças, revisão de arquitetura | Shostack (2014); IEEE CSD 10 flaws (2014) | DFD com fronteiras; registro de ameaças com destino |
| Código | padrão de codificação, funções proibidas, revisão, SAST no commit | SEI CERT; Howard (2007); OWASP Code Review | código revisado com achados tratados |
| Testes | SAST completo, DAST, SCA, fuzzing, intrusão | NIST SP 800-115 (2008); WSTG | evidência com cobertura declarada; triagem |
| Deploy e manutenção | cadeia de suprimentos, configuração, detecção, resposta, gestão de vulnerabilidades | SLSA (2023); NIST SP 800-61; SP 800-40 r4 | incidente vira requisito do próximo ciclo |

## Princípios
| Princípio | Regra | Fonte |
|---|---|---|
| Pushing left | a atividade entra na fase mais cedo em que é decidível | Janca (2020); Boehm (1981) |
| Gate proporcional | bloqueia o novo e grave; o resto vira dívida com dono e prazo | SDL *Final Security Review*; prática DevSecOps |
| Seguro por padrão (SD3+C) | design, padrão e deploy seguros; configuração de fábrica é a restrita | Howard e Lipner (2006) |
| Automatizar o verificável | o que a máquina pode checar não depende de atenção humana | DevOps *Three Ways* (Kim et al., 2016) |
| Retorno de produção | achado e incidente voltam ao desenvolvimento como requisito | shift-right; SSDF RV |
| Medir o programa | tempo de correção, taxa de escape, cobertura dos gates | DORA (Forsgren, Humble e Kim, 2018) |

## Os modelos nomeados — e a natureza de cada um
| Modelo | Natureza | O que dá |
|---|---|---|
| Microsoft SDL (Howard e Lipner, 2006) | prescrição | fases com atividades obrigatórias, bug bar, revisão final; origem do SD3+C |
| Touchpoints (McGraw, 2006) | por artefato | sete pontos de contato: revisão de código, análise de risco de arquitetura, intrusão, testes por risco, casos de abuso, requisitos, operações |
| OWASP SAMM v2 (2020) | maturidade | 5 funções, 15 práticas, 3 níveis; mede onde se está e planeja |
| BSIMM (desde 2009) | observação | ~120 atividades vistas em dezenas de organizações; compara |
| NIST SSDF, SP 800-218 (2022) | resultado esperado | PO, PS, PW, RV; exigido por contrato após a EO 14028 |

Frase: SDL e SSDF dizem o quê; SAMM mede; BSIMM compara; Touchpoints organiza por artefato. Tratá-los como sinônimos é erro.

## DevSecOps — o que é além das ferramentas
Shift-left (SAST, SCA e segredos no pull request; modelagem no refinamento) **e** shift-right (DAST, detecção, bug bounty, incidente de volta ao backlog): um sem o outro deixa buraco. Pipeline típico: pré-commit (segredos, lint) → pull request (SAST diferencial, SCA, IaC) → build (sanitizadores, testes de abuso, checksec) → noturno (SAST completo, DAST autenticado, fuzz) → release (intrusão, revisão de arquitetura) → produção (bug bounty, gestão de vulnerabilidades). Cultura: campeões de segurança com tempo reservado, treinamento ancorado em achado real do próprio repositório, *blameless postmortem*, segurança que diz "sim com condições". Métricas que importam: tempo de correção por severidade, taxa de escape, cobertura dos gates, dívida de segurança e idade. Métricas que enganam: número de achados, número de ferramentas, horas de treinamento.

## Exigências externas
EO 14028 (2021) → SBOM e autoatestação SSDF (CISA, 2023); Cyber Resilience Act (UE, 2024): segurança por design, tratamento de vulnerabilidades, notificação em 24 h; PCI DSS 4.0 requisito 6; ISO/IEC 27001:2022 A.8.25 e A.8.28; NIST CSF 2.0 (2024) função *Govern*. O programa precisa produzir evidência, não só executar.

## No exemplo da API de pagamentos
- Requisitos: "transferência acima de X exige segundo fator" nasce da modelagem e já tem teste.
- Design: DFD com fronteiras; revisão contra os 10 flaws; ADR por decisão.
- Código: Brakeman/gosec/Semgrep no pré-commit; consulta parametrizada; funções proibidas em C.
- Testes: SAST no PR, DAST noturno autenticado, fuzz do decodificador, intrusão por release com duas contas.
- Operação: imagem assinada com proveniência; Dependency-Track cruzando SBOM; incidente vira regra de SAST.

## Erros a não cometer
- DevSecOps não é "scanner no pipeline"; é fluxo, retorno e cultura.
- Shift-left não dispensa o lado direito.
- Segurança não é fase; é atividade em toda fase com gate entre elas.
- Conformidade é piso e evidência, não segurança.
- Gate que bloqueia tudo é desligado na primeira urgência.
- Pushing left é estratégia, não técnica.

## Três referências para citar
- Howard e Lipner (2006), *The Security Development Lifecycle* — SDL, SD3+C.
- Boehm (1981), *Software Engineering Economics* — custo por fase.
- NIST SP 800-218 (2022), SSDF — a referência contratual atual; Forsgren, Humble e Kim (2018) para métricas.

**Ponte.** Requisitos produzem a modelagem de ameaças (ponto 2); a manutenção devolve o incidente como requisito (ponto 10 → 1).
