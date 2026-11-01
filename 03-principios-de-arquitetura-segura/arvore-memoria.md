# Árvore de memória — Ponto 3

Reconstrução de cabeça dos princípios de arquitetura e projeto seguros. Cada nó
puxa o próximo; as **juntas** (⟪ ⟫) são a frase-ponte que torna a ordem
inevitável. Decore a espinha; os ramos (P = princípios, T = técnicas,
M = mecanismos) penduram nela.

Espinha mínima:
`flaw ≠ bug → 4 fundamentos → 8 princípios (1975) → releituras → 6 técnicas → exemplo → síntese`

```
                        ┌──────────────────────────┐
                        │ ARQUITETURA E PROJETO SEG.│
                        └────────────┬─────────────┘
                                     ▼
              FALHA DE DESIGN ≠ BUG — o código faz o que o projeto mandou,
              e o projeto estava errado (McGraw 06: ~metade dos defeitos)
                                     │
                                     ├── corrige-se REDESENHANDO (custo por fase: Boehm 81)
                                     ├── PRINCÍPIO, não checklist: vale para falha ainda não catalogada
                                     └── taxonomia: fundamento → princípio → técnica → mecanismo
                                          (RBAC é mecanismo; menor privilégio é princípio)
                                     │
           ⟪ junta: antes das regras, o que elas pressupõem? ⟫
                                     ▼
                    FUNDAMENTOS (4 premissas)
        CONFIANÇA É DECISÃO EXPLÍCITA, não estado padrão   (Kindervag 10 "centro mole"; NIST 800-207)
        A VIOLAÇÃO VAI ACONTECER em algum lugar             (assume breach → conter, blast radius)
        SEGURANÇA = MECANISMO PEQUENO E SEMPRE INVOCADO     (Anderson 72: monitor de referência; TCB)
        QUEM OPERA É PARTE DO SISTEMA                      (Adams/Sasse 99: controle inusável é desligado)
                                     │
           ⟪ junta: que regras decorrem disso? as de 1975, ainda ⟫
                                     ▼
                    8 PRINCÍPIOS — Saltzer & Schroeder 1975  (+2 imperfeitos)
        1 ECONOMIA DE MECANISMO       pequeno e inspecionável      → TCB mínima · middleware único
        2 PADRÕES À PROVA DE FALHA    permissão, não exclusão      → default deny · fail SECURE · flag off
        3 MEDIAÇÃO COMPLETA           todo acesso verificado       → monitor de ref. · PDP/PEP · sem cache de authz (TOCTOU)
        4 DESENHO ABERTO              não depende do segredo       → Kerckhoffs 1883 · cripto padrão · cofre p/ chave
        5 SEPARAÇÃO DE PRIVILÉGIOS    duas chaves > uma            → MFA · separação de deveres (Clark/Wilson 87) · revisor
        6 MENOR PRIVILÉGIO            mínimo necessário            → privsep OpenSSH (Provos 03) · IAM por função · sem root
        7 MENOR MECANISMO COMUM       pouco compartilhado          → isolamento por tenant · Spectre 19 como aviso
        8 ACEITABILIDADE PSICOLÓGICA  uso correto é o fácil        → SSO · passkeys · 800-63B sem troca periódica
        + FATOR DE TRABALHO (bcrypt/Argon2 · rate limit) · REGISTRO DE COMPROMETIMENTO (log íntegro, R do STRIDE)
          vocabulário: fail-safe (física: destranca) ≠ fail secure (software: nega)
                                     │
           ⟪ junta: o que mudou com rede, nuvem, terceiros? só o vocabulário ⟫
                                     ▼
                    RELEITURAS CONTEMPORÂNEAS (linhagem)
        DEFESA EM PROFUNDIDADE   camadas INDEPENDENTES    = violação acontece + 6 + 3   (NSA IATF 00; Viega/McGraw 01)
        ASSUME BREACH            até onde chega daqui?    = violação acontece + 6 + 7 + registro (Microsoft 2010s)
        ZERO TRUST               acesso por identidade/sessão, não por rede = confiança explícita + 3 + 6 + 2
                                 (Kindervag 10 → BeyondCorp 14 → NIST 800-207 20: 7 tenets, PDP/PEP)
        SEGURO POR PADRÃO/DESIGN fábrica segura; decidido no projeto = 2 + 8 + 1 (Howard/Lipner 06 SD3+C; CISA 23)
        SEPARAÇÃO DE DEVERES     executa ≠ confere = 5 (Clark/Wilson 87)
        ELO MAIS FRACO / NÃO CONFIAR NO CLIENTE = confiança explícita + 3 (Viega/McGraw 01; IEEE CSD 14 #1)
                                     │
           ⟪ junta: regra diz o que não violar; falta o MÉTODO repetível ⟫
                                     ▼
                    6 TÉCNICAS (nome + fonte → mecanismos → saída)
        MONITOR DE REFERÊNCIA / CHECK POINT  (Anderson 72; Yoder/Barcalow 97)  → gateway · OPA · policy as code → toda seta passa por 1 nó
        PADRÕES DE SEGURANÇA  (Yoder/Barcalow 97; Schumacher 06)  Single Access Point · Check Point · Roles · Session
                              · Full View w/ Errors · Limited View · Secure Access Layer → vocabulário de revisão
        SEPARAÇÃO DE PRIVILÉGIOS / COMPARTIMENTAÇÃO  (Provos/Friedl/Honeyman 03)  → contêiner sem root · seccomp · conta por ambiente → mapa de compartimentos
        ARQUITETURA ZERO TRUST  (NIST 800-207)  policy engine + administrator = PDP; PEP no caminho
                              variantes: identidade · microssegmentação · SDP → mTLS · proxy ciente de identidade · SPIFFE
        REVISÃO CONTRA 10 FLAWS  (IEEE CSD 14)  1 confiança 2 authn inviolável 3 authz após authn 4 dado≠controle
                              5 validação 6 cripto 7 dado sensível 8 usuários 9 terceiros/superfície 10 mudança → ADR por item
        REQUISITOS VERIFICÁVEIS  (OWASP ASVS V1; NIST 800-160)  → backlog c/ nível e teste → ativo→requisito→controle→teste
                                     │
           ⟪ junta: aplicar tudo num caso: a API de pagamentos do ponto 2 ⟫
                                     ▼
                    EXEMPLO — cada mitigação nomeia ameaça → princípio → técnica → mecanismo → teste
        troca de id → mediação completa → Check Point → middleware → 403
        dependência vulnerável → menor privilégio → compartimentação → só-leitura → lê, não move
        credencial interna → confiança explícita → zero trust → mTLS + PDP/PEP
        operador interno → separação de privilégios/deveres → 2º fator + 2º operador + log assinado
        canal do parceiro → desenho aberto + profundidade → TLS + assinatura + minimizar → fail secure
        rajada → fator de trabalho → rate limit + serviços separados
        config → seguro por padrão → flag off · MFA on · caminho seguro é o fácil
                                     │
                                     ▼
                    ERROS A NÃO COMETER
        RBAC não é princípio · zero trust não é produto · 2 antivírus ≠ profundidade
        fail-safe ≠ fail secure · aberto ≠ publicar tudo · SAST não acha flaw
```

Molde repetido de cada princípio (decore uma vez, aplique oito):
**regra (1975) → o que viola → técnica (fonte) → mecanismo → exemplo**.
