# Árvore de memória — Ponto 5

Reconstrução de cabeça da codificação segura e dos erros de memória. Cada nó
puxa o próximo; as **juntas** (⟪ ⟫) são a frase-ponte. Decore a espinha; os
ramos (P = princípios, T = técnicas, M = mecanismos) penduram nela.

Espinha mínima:
`bug de segurança ≠ bug comum → 4 fundamentos → erros de memória (história: ataque ↔ mitigação) → 8 princípios → 6 técnicas → exemplo → erros`

```
                        ┌────────────────────────────────┐
                        │ CODIFICAÇÃO SEGURA · MEMÓRIA    │
                        └───────────────┬────────────────┘
                                        ▼
              BUG DE SEGURANÇA ≠ BUG COMUM — o atacante dá ao bug probabilidade 1
              A LINGUAGEM DECIDE A CLASSE — C/C++: UB; Java/Go/Rust: exceção ou recusa
              EVIDÊNCIA: ~70% das vulns = memória (Miller/MS 19; Chromium 20) → CISA/NSA 23 roadmaps
                                        │
           ⟪ junta: antes das regras, o que elas pressupõem? ⟫
                                        ▼
                    FUNDAMENTOS (4)
        LINGUAGEM DEFINE A CLASSE DE ERRO POSSÍVEL   escolha de linguagem = decisão de segurança
        UB É CONTRATO ROMPIDO                        compilador assume que não acontece; "passou no teste" não vale
        TODA ENTRADA É HOSTIL, INCLUSIVE A INTERNA    nunca confiar no campo de tamanho que veio com o dado
        REGRA VERIFICÁVEL VENCE CONSELHO             identificador por regra + ferramenta que cobra
                                        │
           ⟪ junta: a classe que define a disciplina ⟫
                                        ▼
                    ERROS DE MEMÓRIA (CWE) — o que é · história · regra CERT · ferramenta · mitigação
        ESTOURO DE BUFFER 120/121/122   Morris 88 (gets) · Aleph One 96 shellcode · off-by-one · formato 134 (%n)
                 R: ARR30-C STR31-C FIO47-C · funções proibidas (Howard 07 banned.h) · strlcpy/snprintf
                 F: ASan · SAST · fuzz        Mit: canário StackGuard 98 · NX/DEP 04 · FORTIFY
        LEITURA FORA 125                Heartbleed 14: copiou o tamanho que o cliente declarou → chaves vazadas
        USE-AFTER-FREE 416 (+415 double) classe dominante em navegadores; vtable sequestrada
                 R: MEM30-C MEM31-C · anular após free · smart pointers   Mit: PartitionAlloc · MTE   Rust: borrow checker
        ESTOURO DE INTEIRO 190 (195 sinal, 197 truncar)  n*size dá a volta → malloc pequeno → heap overflow
                 R: INT30/31/32-C · verificar antes · __builtin_mul_overflow · size_t   F: UBSan   domínio: centavos negativos
        NÃO INICIALIZADA 457 · NULO 476  R: EXP33-C EXP34-C   F: MSan
        CORRIDA 362 · TOCTOU 367         R: FIO45-C (opere no descritor, não no nome)   F: TSan
                 ✗ memória segura ≠ concorrência segura (Go/Java têm corrida; Rust não)
                                        │
        HISTÓRIA  shellcode → NX/DEP → ret2libc 97 / ROP (Shacham 07) → ASLR (PaX 01) + canários → vazamento+ROP
                  → CFI (Abadi 05) · shadow stack/CET · MTE → LINGUAGEM SEGURA (resposta estrutural)
                  cada mitigação eleva o fator de trabalho; nenhuma corrige o bug
                                        │
           ⟪ junta: o que o código faz com qualquer entrada, em qualquer linguagem? ⟫
                                        ▼
                    8 PRINCÍPIOS
        1 VALIDAR NA BORDA, POR PERMISSÃO    "analise, não valide": tipo que só existe válido (Centavos, IdDeConta)
        2 SEPARAR DADO DE INSTRUÇÃO          printf("%s", x) · execve · consulta parametrizada
        3 FALHAR FECHADO E SEM VAZAR         ERR33-C: trate todo retorno · nega · libera · log sem segredo · fail-open é A10 2025
        4 ARITMÉTICA VERIFICADA              CERT INT
        5 POSSE E TEMPO DE VIDA EXPLÍCITOS   um dono, um free
        6 MENOR PRIVILÉGIO NO CÓDIGO         abre a porta como root, desce (Provos 03)
        7 NENHUM SEGREDO NO CÓDIGO           cofre em runtime · varredura no pipeline
        8 COMPILAR COM TUDO LIGADO           -fstack-protector-strong -D_FORTIFY_SOURCE=2 -fPIE -z relro,now -fcf-protection -Werror · checksec
                                        │
           ⟪ junta: regra dita; método nomeado e ferramenta que cobra ⟫
                                        ▼
                    6 TÉCNICAS
        PADRÃO COM REGRA IDENTIFICADA   SEI CERT C (Seacord 08/16) · CERT Java · MISRA C 2012 · CWE como vocabulário → SAST com perfil
        FUNÇÕES PROIBIDAS               Howard 07 banned.h: strcpy strcat sprintf gets → não compila · Anexo K / strlcpy
        SANITIZADORES EM TESTE          Serebryany 12: ASan UBSan MSan TSan · 2–3× · nunca em produção · falha bloqueia merge
        ENDURECIMENTO DO BINÁRIO        ASLR (PIE) · canários · NX · RELRO · CFI/CET · checksec no pipeline
        LINGUAGEM SEGURA POR PADRÃO     CISA/NSA 23 · Rust onde C era obrigatório (Linux 22; Android 76%→24%) · unsafe/FFI isolados e revisados
                                        ✗ não resolve lógica, authz, injeção, segredo, dependência
        REVISÃO ORIENTADA A SEGURANÇA   OWASP Code Review Guide 17 · Howard/LeBlanc 02: siga o DADO, não a estrutura
                                        │
           ⟪ junta: o mesmo sistema, na linha do código ⟫
                                        ▼
                    EXEMPLO — API de pagamentos
        decodificador ISO 8583 em C → comprimento vs buffer E vs bytes recebidos → banned.h → flags+checksec → ASan/UBSan → 1º a migrar p/ Rust
        centavos → tipo Centavos + aritmética verificada → estouro vira erro de validação
        erro → nega, libera, log sem cartão, id de correlação    log → printf("%s") + mascaramento
        arquivo → fstat no descritor, O_NOFOLLOW    segredo → cofre + varredura    processo → desce privilégio, seccomp
        revisão → template: entradas · usos · funções perigosas · erros · segredos; unsafe = item obrigatório
                                        │
                                        ▼
                    ERROS A NÃO COMETER
        linguagem segura ≠ segurança · ASLR/DEP ≠ correção · "passou no teste" ≠ sem UB · memória ≠ concorrência
        strncpy ≠ seguro · validar ≠ sanitizar · aviso ≠ cosmético · "C com cuidado" contra 70% · sanitizador em produção
```

Molde repetido de cada erro (decore uma vez, aplique seis):
**o que é → história → regra CERT → ferramenta → mitigação da plataforma → em linguagem segura**.
