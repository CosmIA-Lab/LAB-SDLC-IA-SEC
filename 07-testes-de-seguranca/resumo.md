# Ponto 7 — Testes de segurança (SAST, DAST, SCA, fuzzing) · resumo para releitura

**Em uma frase.** O teste mostra presença de defeitos, nunca ausência (Dijkstra, 1970); em segurança, o requisito é negativo e exige caso de abuso (McDermott e Fox, 1999); cada técnica — estática, dinâmica, composição, fuzzing, intrusão — observa o sistema por uma janela, e o programa de testes é a combinação das janelas com triagem e cobertura declarada.

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
- **Teste mostra presença, não ausência**: "SAST limpo" = padrões conhecidos não encontrados; o relatório diz o que não cobriu.
- **Requisito negativo exige teste negativo**: ameaça (ponto 2) → requisito → teste que espera 403.
- **Cada técnica, uma janela**: caixa branca/cinza/preta × estático/dinâmico × manual/automático; combinar, não substituir.
- **Achado sem triagem é ruído** (Johnson et al., 2013): falso positivo em volume mata a ferramenta; dono, prazo, decisão.

## Mapa
MERMAID

## As janelas
| Técnica | O que é | Vê bem | Não vê | Fonte | Mecanismo |
|---|---|---|---|---|---|
| SAST | análise do código sem executar: léxico → AST → *taint* (fonte → destino sem neutralizador) → interpretação abstrata → execução simbólica | injeção, API perigosa, memória por padrão, segredo; todo o código, com linha | configuração, autorização, dependência, runtime, reflexão; falsos positivos 30–70% | Chess e West (2007); Livshits e Lam (2005); CodeQL (2016); Cousot (1977); King (1976) | Semgrep, CodeQL, Fortify, Brakeman, gosec; diferencial no PR, completo no noturno |
| DAST | caixa preta contra a aplicação rodando: rastreia, envia cargas, lê respostas | XSS refletido, SQLi por erro/tempo, cabeçalhos, configuração | linha, estados profundos, XSS armazenado, autorização, lógica; lento, destrutivo | NIST SP 800-115 (2008); WSTG 4.2 | ZAP, Burp, Nuclei; autenticado; OpenAPI; ambiente de teste |
| IAST / RASP | agente no runtime observa fluxo durante testes / bloqueia em produção | linha + caminho real; poucos FP | só o que os testes exercitam; RASP é controle, não teste | — | agente por linguagem |
| Teste de intrusão | manual, com método e tempo limitado | autorização, lógica, encadeamento, o que o modelo marcou | pouca cobertura por hora | NIST SP 800-115; PTES (2014); WSTG | escopo do modelo; duas identidades; reteste; bug bounty depois do óbvio |
| SCA | inventário de dependências × bases de CVE e licenças | CVE conhecida, licença, abandono | inédita, pacote malicioso (ponto 8), alcançabilidade | NVD/OSV; SBOM; VEX (CISA, 2022) | Dependency-Check, Renovate, Trivy, govulncheck |
| Fuzzing | entradas geradas ou mutadas, guiadas por cobertura, com sanitizadores | memória, parsing, exceção, laço, ReDoS, propriedade violada | lógica, autorização | Miller et al. (1990); AFL (2013); Böhme et al. (2016); QuickCheck (2000); Manès et al. (2019) | AFL++, libFuzzer, Jazzer, OSS-Fuzz; harness pequeno; corpus versionado; contínuo |
| Testes escritos | casos de abuso no arcabouço de testes | o específico do sistema: matriz de autorização, regressão, arquitetura | o que não foi modelado | McDermott e Fox (1999); ArchUnit | 403 esperado; toda vuln corrigida vira teste |

## Anatomia de um falso positivo de taint
Fonte `params[:nome]` → destino consulta sem parametrização: verdadeiro. `Random` para nome de arquivo temporário em diretório privado: a ferramenta marca "aleatoriedade insegura" porque a lista de destinos inclui caminho — falso positivo; supressão com motivo, regra ajustada. Causas: framework não modelado, neutralizador caseiro, caminho dependente de valor, abstração conservadora. Medir antes de adotar: precisão × revocação com Juliet (NIST SAMATE) e OWASP Benchmark; duas semanas em observação.

## Onde cada janela entra no ciclo
Pré-commit (segundos): lint de segurança, segredos → PR (minutos): SAST diferencial, SCA, IaC → build: sanitizadores, casos de abuso, checksec → noturno (horas): SAST completo, DAST autenticado, fuzz → release: intrusão, revisão de arquitetura → produção: bug bounty, gestão de vulnerabilidades. Cada falha pega pela técnica mais barata capaz (Boehm, 1981).

## APIs e distribuído
Contrato OpenAPI como roteiro; matriz de autorização gerada (outro tenant, sem escopo, `aud` errado, expirado, sem token, `X-User-Id`); sequência e estado (RESTler, 2019); fila como fonte de taint; infraestrutura: Prowler, kube-bench (CIS); red team com ATT&CK como catálogo.

## Triagem e programa
Confirmar (reproduzir) → classificar (CVSS 3.1/4.0 como vocabulário, não oráculo; EPSS; bug bar) → decidir (corrigir com prazo e regressão; aceitar com dono e motivo; falso positivo com regra ajustada) → fechar (reteste). Princípios do programa: rastreabilidade ao modelo; técnica mais barata; gate proporcional; triagem como trabalho; cobertura declarada; métricas (tempo de correção, taxa de escape, FP por regra). Outras varreduras: segredos (gitleaks), IaC (Checkov), imagem (Trivy), nuvem (Prowler).

## No exemplo da API de pagamentos
- SAST: concatenação na busca → parâmetro + regressão.
- DAST: CSP ausente e XSS refletido na página de erro.
- SCA: biblioteca XML transitiva com CVE → atualizada; alcançabilidade adotada.
- Fuzz: comprimento negativo no decodificador → INT31-C; entrada vira teste.
- Intrusão: IDOR com duas contas → middleware + matriz de autorização.
Cinco janelas, cinco achados distintos, cinco regressões.

## Erros a não cometer
- SAST limpo ≠ código seguro.
- DAST não cobre autorização nem lógica.
- SCA não é segurança da cadeia de suprimentos.
- Fuzzing não é só para C.
- Teste de intrusão anual não basta: contínuo, guiado pelo modelo.
- Cobertura funcional alta não mede segurança.

## Três referências para citar
- NIST SP 800-115 (2008) e OWASP WSTG 4.2 (2020) — método e roteiro.
- Livshits e Lam (2005) — análise de taint; Chess e West (2007).
- Miller, Fredriksen e So (1990) — fuzzing; Zalewski (2013) AFL; Manès et al. (2019).

**Ponte.** A SCA aponta a dependência com CVE; o que ela não vê — pacote envenenado, build comprometido — é a cadeia de suprimentos (ponto 8).
