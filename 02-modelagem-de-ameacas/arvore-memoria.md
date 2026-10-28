# Árvore de memória — Ponto 2

Reconstrução de cabeça da modelagem de ameaças e da superfície de ataque. Cada
nó puxa o próximo; as **juntas** (⟪ ⟫) são a frase-ponte que torna a ordem
inevitável. Decore a espinha; os ramos (P = princípios, T = técnicas,
M = mecanismos) penduram nela.

Espinha mínima:
`o que pode dar errado → ameaça≠vuln≠risco → 4 fundamentos → 4 perguntas → superfície → outras abordagens`

```
                        ┌──────────────────┐
                        │ MODELAGEM AMEAÇAS │
                        └────────┬─────────┘
                                 ▼
              "O QUE PODE DAR ERRADO?" — pergunta da fase de DESIGN
                                 │
                                 ├── AMEAÇA ≠ VULNERABILIDADE ≠ RISCO (NIST 800-30)
                                 │     ameaça = o que pode acontecer
                                 │     vuln   = por onde
                                 │     risco  = prob × impacto
                                 │
                                 ├── MÉTODO, não intuição
                                 │     "pense como atacante" = conselho, não método (Shostack 14)
                                 │
           ⟪ junta: antes do método, o que ele pressupõe? ⟫
                                 ▼
                    FUNDAMENTOS (4 premissas)
        toda ameaça VIOLA UMA PROPRIEDADE  (CIA + authn·authz·não-repúdio = 6)
        ameaça NÃO SE ELIMINA, DECIDE-SE
        ameaça CONCENTRA-SE NA FRONTEIRA DE CONFIANÇA
        o ATACANTE ESCOLHE A PORTA (defensor precisa de todas)
                                 │
           ⟪ junta: como percorrer o sistema sem deixar ameaça de fora? ⟫
                                 ▼
                    4 PERGUNTAS (Shostack 2014)
                                 │
  1 O QUE CONSTRUÍMOS? ── desenho por onde o dado passa
        P: fronteira explícita · nível certo de detalhe · sistema real
        T: DFD (DeMarco 79) + fronteira de confiança (Shostack 14)
           elementos: entidade ext · processo · fluxo · armazenamento · fronteira
        M: Threat Modeling Tool · Threat Dragon · pytm
        → DFD com fronteiras
                                 │
        ⟪ junta: sei ONDE procurar; O QUE procurar em cada lugar? ⟫
                                 │
  2 O QUE PODE DAR ERRADO? ── ameaças por elemento
        P: estrutura não intuição · cobertura por elemento · ameaça ligada ao desenho
        T: STRIDE (Kohnfelder/Garg, MS 99) — 6 letras = 6 propriedades
              S spoofing→authn · T tampering→integridade · R repúdio→não-repúdio
              I disclosure→confid. · D DoS→disponib. · E elevação→authz
           STRIDE POR ELEMENTO (Shostack 14)
              ent.externa: S R · processo: STRIDE · fluxo: T I D · store: T I D (R se log)
           ATTACK TREE (Schneier 99) — raiz = objetivo, ramos E/OU
           (CAPEC · ATT&CK · Top 10 = bibliotecas, não técnica)
        M: geração por elemento · jogo EoP (Shostack 2010) · registro
        → lista de ameaças ligada ao diagrama
                                 │
        ⟪ junta: dezenas de ameaças; o que fazer com cada uma e qual primeiro? ⟫
                                 │
  3 O QUE FAZEMOS? ── cada ameaça com destino
        P: nenhuma sem decisão · mitigação é requisito · risco ordena não elimina · aceitar exige dono
        T: 4 DESTINOS (Shostack 14): mitigar · eliminar · transferir · aceitar
           MITIGAÇÃO POR CATEGORIA: S→authn · T→integridade · R→log/assinatura
              · I→cripto/authz · D→rate limit · E→authz/least priv
           RISCO = prob × impacto (NIST 800-30) · DREAD (Howard/LeBlanc 03, abandonado) · bug bar
        M: registro com destino · backlog · risco aceito
        → mitigação vira REQUISITO com critério de aceite
                                 │
        ⟪ junta: decidir não é provar — como saber que cobriu e que entrou? ⟫
                                 │
  4 FIZEMOS UM BOM TRABALHO? ── modelo validado e vivo
        P: cobertura verificável · mitigação prova-se no teste · modelo acompanha desenho · do time
        T: checklist (diagrama·cobertura·destino·teste) · teste como prova (NIST 800-115)
        M: modelo versionado c/ código · revisão no pipeline · id ameaça → teste
        → rastreabilidade: ameaça → requisito → controle → teste
                                 │
   ⟪ junta: as 4 etapas assumem o contorno; o contorno é decisão → quantas portas? ⟫
                                 ▼
                    SUPERFÍCIE DE ATAQUE
        def: métodos · canais · itens de dado (Manadhata/Wing 2011)
        P: redução antes de proteção · exposição define prioridade · padrão fechado
           · inclui o que você não escreveu
        T: métrica (Manadhata/Wing 11) · RASQ (Howard/Pincus/Wing 03) · redução (Howard 03, MS SDL)
        M: OpenAPI inventário · nmap · SCA · feature flag off · API gateway
        → inventário por exposição + o que foi removido
                                 │
   ⟪ junta: STRIDE é a via principal, não a única ⟫
                                 ▼
                    OUTRAS ABORDAGENS
        PASTA (UcedaVélez/Morana 15) — processo, 7 estágios, parte do negócio
        LINDDUN (Deng et al. 11) — taxonomia de privacidade, mesmo DFD
        NIST 800-154 — centrada em dados
        Manifesto (2020) — 4 perguntas, contínuo, do time
```

Molde repetido de cada etapa (decore uma vez, aplique quatro):
**objetivo → princípios → técnicas → mecanismos → saída**.
