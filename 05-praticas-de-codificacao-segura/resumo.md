# Ponto 5 — Práticas de codificação segura e erros de memória · resumo para releitura

**Em uma frase.** Codificação segura impede o código de trair o projeto; sua classe definidora é o erro de memória — em C e C++ o programador é a única barreira entre a entrada hostil e a memória, e cerca de 70% das vulnerabilidades corrigidas por Microsoft e Chromium são dessa classe (Miller, 2019) — e a resposta é regra com identificador (SEI CERT), ferramenta que a cobra (sanitizadores), mitigação da plataforma e, estruturalmente, linguagem segura em memória (CISA e NSA, 2023).

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
- **A linguagem define a classe de erro possível**: C/C++ têm comportamento indefinido; Java, Go, C#, Rust produzem exceção ou recusa de compilar. Escolha de linguagem é decisão de segurança.
- **Comportamento indefinido é contrato rompido**: o compilador assume que não acontece; "passou no teste" não prova nada sem sanitizador.
- **Toda entrada é hostil, inclusive a interna**: o campo de tamanho do pacote é dado do pacote (Heartbleed).
- **Regra verificável vence conselho**: CERT MEM30-C é checável; "tenha cuidado" não (Howard e LeBlanc, 2002; Seacord, 2013).

## Mapa
MERMAID

## Erros de memória — o que é, história, regra, ferramenta, mitigação
| Erro (CWE) | Como | História | Regra CERT | Ferramenta | Mitigação da plataforma |
|---|---|---|---|---|---|
| Estouro de buffer (120/121/122); off-by-one; string de formato (134) | cópia além do fim; `printf(entrada)` com `%n` | Morris 1988 (gets); Aleph One 1996 | ARR30-C, STR31-C, FIO47-C; funções proibidas (Howard, 2007): strcpy, strcat, sprintf, gets | ASan; fuzz | canários (StackGuard, 1998); NX/DEP (2004); _FORTIFY_SOURCE |
| Leitura fora dos limites (125) | copia o tamanho que o cliente declarou | Heartbleed 2014: 64 KB, chaves privadas | comprimento declarado ≤ recebido | ASan | — |
| Use-after-free (416); double free (415) | bloco realocado; vtable controlada | dominante em navegadores | MEM30-C, MEM31-C; um dono, um free; smart pointers | ASan com quarentena | PartitionAlloc; MTE; Rust recusa compilar |
| Estouro de inteiro (190; 195 sinal; 197 truncar) | n × sizeof dá a volta → malloc pequeno | — | INT30/31/32-C; `__builtin_mul_overflow`; size_t | UBSan | — |
| Não inicializada (457); nulo (476) | lixo decide; deref nulo | — | EXP33-C, EXP34-C | MSan | — |
| Corrida (362); TOCTOU (367) | verificar e usar separados | — | FIO45-C: opere no descritor, não no nome | TSan | — |

Linha do tempo: shellcode → NX/DEP → return-to-libc (1997) / ROP (Shacham, 2007) → ASLR (PaX, 2001) + canários → vazamento + ROP → CFI (Abadi et al., 2005), shadow stack/CET, MTE → linguagem segura. Cada mitigação eleva o fator de trabalho; nenhuma corrige o bug. Segurança de memória ≠ segurança de concorrência (Go e Java têm corrida; Rust não).

## Erros fora da memória
Cripto mal usada (aleatoriedade fraca CWE-330; comparação não constante CWE-208; alg do token lido do token); ReDoS (CWE-1333); desserialização nativa do cliente (CWE-502); caminho montado com entrada (CWE-22); segredo em log e erro (CWE-532, 209); conversão implícita em decisão (`"1" == 1`). Todos: o código confiando em algo que não verificou.

## Oito princípios
| Princípio | Regra |
|---|---|
| Validar na borda, por permissão | "analise, não valide": tipo que só existe válido (`Centavos`, `IdDeConta`) |
| Separar dado de instrução | `printf("%s", x)`; execve com argumentos; consulta parametrizada |
| Falhar fechado e sem vazar | ERR33-C: trate todo retorno; nega, libera, log sem segredo; fail-open é A10 de 2025 |
| Aritmética verificada | CERT INT |
| Posse e tempo de vida explícitos | um dono, um free |
| Menor privilégio no código | abre como root, desce antes da 1ª mensagem (Provos et al., 2003) |
| Nenhum segredo no código | cofre em runtime; varredura no pipeline |
| Compilar com tudo ligado | `-fstack-protector-strong -D_FORTIFY_SOURCE=2 -fPIE -pie -Wl,-z,relro,-z,now -fcf-protection -Werror`; checksec |

## Antes e depois
`memcpy(buf, msg, len)` → `if (len > sizeof buf || len > recebido) return ERRO;` · `syslog(LOG_INFO, msg)` → `syslog(LOG_INFO, "%s", msg)` · `total = n * sizeof(Item)` → `if (__builtin_mul_overflow(n, sizeof(Item), &total)) return ERRO;` · `access(); fopen()` → `open(O_NOFOLLOW); fstat(fd)`.

## Técnicas
| Técnica | Fonte | Mecanismo |
|---|---|---|
| Padrão com regra identificada | SEI CERT C (2016); MISRA C (2012); CERT Java | SAST com perfil; checklist de revisão |
| Funções proibidas | Howard (2007), banned.h | não compila; Anexo K; strlcpy |
| Sanitizadores em teste | Serebryany et al. (2012): ASan, UBSan, MSan, TSan | `-fsanitize` na suíte e no fuzz; nunca em produção |
| Endurecimento do binário | ASLR (PaX, 2001); CFI (Abadi, 2005) | flags + checksec no pipeline |
| Linguagem segura por padrão | CISA/NSA (2023); Android 76% → 24% | Rust onde C era obrigatório; unsafe e FFI isolados; não resolve lógica, authz, injeção |
| Revisão orientada a segurança | OWASP Code Review Guide (2017); Howard e LeBlanc (2002) | siga o dado, não a estrutura; template no PR |
| Quando a linguagem não é escolha | Janca (2020), cap. 4 | framework suportado, versão atual, SCA a cada build; 7 regras para código em C |

## No exemplo da API de pagamentos
- Decodificador ISO 8583 em C: comprimento vs buffer e vs bytes recebidos; banned.h; flags + checksec; ASan/UBSan; primeiro a migrar para Rust.
- Centavos: tipo que só existe válido + aritmética verificada → estouro vira erro de validação.
- Erro: nega, libera, log sem cartão, id de correlação.
- Processo: desce privilégio; contêiner sem root; seccomp.

## Erros a não cometer
- Linguagem segura em memória não resolve segurança: fecha uma classe.
- ASLR/DEP são mitigação, não correção.
- "Passou no teste" não exclui comportamento indefinido.
- strncpy não é a versão segura de strcpy.
- Validar não é sanitizar.
- Aviso de compilador não é cosmético.

## Três referências para citar
- Seacord (2013), *Secure Coding in C and C++*; SEI CERT C Coding Standard (2016).
- Aleph One (1996), *Smashing the Stack*; Serebryany et al. (2012), AddressSanitizer.
- CISA e NSA (2023), *The Case for Memory Safe Roadmaps*; Miller (2019), ~70%.

**Ponte.** O código que mais erra é o de controle de acesso: autenticação, autorização e sessão (ponto 6).
