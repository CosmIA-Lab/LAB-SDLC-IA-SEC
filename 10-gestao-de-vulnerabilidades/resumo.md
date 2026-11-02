# Ponto 10 — Gestão de vulnerabilidades · resumo para releitura

**Em uma frase.** O processo contínuo que governa o tempo entre uma falha existir e ser corrigida em produção: inventário gerado, descoberta contínua, decisão por risco real (exploração, exposição, impacto — não por nota), correção no prazo com mitigação no intervalo, evidência de fechamento, métricas de tempo, divulgação coordenada com quem acha antes, e a lição que volta ao ciclo.

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
- **A vulnerabilidade é certa; a variável é o tempo**: medir e reduzir o tempo de exposição (Arbaugh, Fithen e McHugh, 2000: janela de vulnerabilidade). Exploração em dias (Mandiant, 2023–24); o problema é a *n-day*, não a *zero-day*.
- **Não se gerencia o que não se conhece**: inventário em três camadas — ativos, software, componentes (SBOM) — automático e com dono. Log4Shell foi crise de inventário.
- **Nota não é risco** (Spring et al., 2021): mais da metade das CVEs é alta ou crítica, menos de 5% exploradas; o CVSS base ignora contexto e exploração.
- **Processo contínuo com ciclo e métrica** (NIST SP 800-40 r4, 2022; BOD 19-02: 15/30 dias; BOD 22-01: KEV).

## Mapa
MERMAID

## Vocabulário
| Sigla | O que é | O que não é |
|---|---|---|
| CVE (MITRE, 1999; CNAs) | identificador de vulnerabilidade pública | severidade; prova de que afeta você |
| NVD (NIST) | enriquecimento: CVSS, CWE, CPE | a única base (OSV, GitHub Advisory) |
| CWE | classe de fraqueza (79 = XSS) | instância |
| CVSS (FIRST) 3.1 / 4.0 | severidade técnica: base (AV AC PR UI S C I A), ameaça, ambiental; ler o vetor | risco |
| KEV (CISA, 2021) | exploração confirmada em campo; prazo obrigatório (BOD 22-01) | completo |
| EPSS (FIRST, 2021) | probabilidade de exploração em 30 dias | severidade |
| SSVC (SEI, 2019; CISA, 2021) | árvore de decisão: exploração, automatizável, impacto técnico, missão → Track, Track*, Attend, Act | nota |
| VEX (CISA, 2022) | afetado / não afetado / corrigido / em investigação, com motivo | substituto da análise própria |
| CSAF (OASIS, 2022) | aviso legível por máquina | — |

Três falhas de gestão: Equifax 2017 (Struts, correção dois meses antes, inventário falhou); WannaCry 2017 (MS17-010, dois meses antes); MOVEit 2023 (zero-day: inventário, mitigação e detecção).

## O ciclo
| Etapa | O que | Princípios e método | Mecanismo |
|---|---|---|---|
| Descobrir | varredura com credencial, agente, imagens, SBOM × CVE contínuo, CSPM, testes (ponto 7), avisos CSAF, relatos externos | contínua ou semanal; cobertura medida; dedup; dono na descoberta | Nessus/Qualys/OpenVAS, Trivy, Dependency-Track, Prowler |
| Priorizar | exploração (KEV, EPSS, exploit, logs) + exposição (internet? alcançável? VEX) + impacto + vetor → classe → prazo | SSVC; prazos escritos (ex.: KEV ou crítica exposta 48 h–7 d; crítica interna 15 d; alta 30 d; resto 90 d); exceção com dono, mitigação e prazo de revisão | árvore de decisão; política |
| Corrigir | patch, atualizar, reconfigurar, substituir; mitigar enquanto não chega (virtual patching, isolamento) — não fecha o item | NIST SP 800-40 r4: teste, anéis, reversão, janela + emergência; imutável: reconstruir, não remendar; por ambiente (servidores, estações, contêineres, nuvem gerenciada, dependências, firmware/OT, terceiros) | Renovate, gerenciador de configuração, imagem nova + admissão, regra de WAF |
| Verificar | nova varredura, SBOM novo, regressão, regra testada | item fecha com evidência | — |
| Relatar | tempo de exposição por classe (mediana, p90), conformidade com prazo, idade dos abertos, cobertura, KEV abertos (= 0), exceções, reincidência | não medir total de achados, total corrigido, nota média | painel |

Nuvem: configuração é vulnerabilidade sem CVE (bucket público, papel excessivo) → CSPM contra CIS; corrigir em código; drift. Programa: papéis (dono do ativo, segurança, PSIRT, gestão), política escrita, conformidade como piso (PCI DSS 4.0: 1 mês; ISO 27001 A.8.8; BOD 22-01; CRA), maturidade (SAMM, BSIMM). Cadência: dia (KEV, act), semana (triagem 1 h), mês (métricas), trimestre (exceções, inventário, exercício de mesa), ano (VDP).

## Divulgação coordenada
ISO/IEC 29147 (2018): receber e publicar; ISO/IEC 30111 (2019): tratar por dentro; CERT/CC CVD Guide (Householder et al., 2017): papéis e problemas; FIRST PSIRT Framework. Debate: full → "responsável" → coordenada (prazo acordado, publicação conjunta); Project Zero 90 dias; CISA 45; explorada → dias. Preparar: security.txt (RFC 9116, 2022); VDP com escopo e safe harbor (BOD 20-01); PSIRT que confirma em 48 h; aviso CSAF com CVE (CNA própria), VEX e crédito; bug bounty só depois de tirar o óbvio. Como consumidor: assinar CSAF e feeds; inventário diz se afeta.

## Ponte com incidente
Vulnerabilidade é condição; incidente é evento. NIST SP 800-61 (r2 2012; r3 2025): preparação, detecção e análise, contenção, erradicação, recuperação, pós-incidente. Detecção vem dos logs dos pontos 4, 6 e 9; contenção é a mitigação pronta; pós-incidente devolve requisito ao ciclo (10 → 1).

## No exemplo — a segunda-feira da CVE
- 9h00: Dependency-Track abre item: XML crítica em duas imagens; dono automático.
- 9h20: SSVC — decodificador Act, 48 h (PoC, mTLS com parceiro, toca dinheiro); relatórios Track + VEX "não alcançável".
- 10h00: mitigação — regra no gateway contra ENTITY + alerta; Falco; logs.
- 10h30: PR do Renovate → pipeline (ponto 7) → build assinado (ponto 8) → homologação → produção em anel; admissão recusa a antiga.
- 14h00: verificação — nenhuma imagem com a versão; regressão; fecha com evidência. Exposição: 5 horas.
- Quinta: pesquisador via security.txt relata IDOR na `v1` que o gateway ainda roteava → confirma em 24 h, desliga rota, aviso, crédito, recompensa → lição para o ponto 9 (catálogo gerado do gateway) e 7 (rota fora do catálogo → 404).

## Erros a não cometer
- CVSS alto ≠ corrigir primeiro.
- Varredura limpa ≠ seguro.
- Aplicar tudo imediatamente sem teste quebra produção; emergência só para KEV e crítica exposta.
- Zero-day não é o problema; n-day é.
- WAF não corrige: mitiga até a correção.
- Exceção aprovada sem prazo é aceitação permanente.

## Três referências para citar
- NIST SP 800-40 rev. 4 (2022) — patching como gestão de risco.
- FIRST CVSS 3.1/4.0 e EPSS; CISA KEV (2021) e SSVC (2021); Spring et al. (2021), *Time to Change the CVSS?*
- ISO/IEC 29147 e 30111; CERT/CC CVD Guide (2017); RFC 9116 (security.txt); NIST SP 800-61.

**Ponte.** O incidente é o requisito do próximo ciclo: pushing left de novo (ponto 1).
