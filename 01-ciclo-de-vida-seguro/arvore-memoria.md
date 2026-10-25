# Árvore de memória — Ponto 1

Reconstrução de cabeça do ciclo de vida seguro. Cada nó puxa o próximo; as
**juntas** (⟪ ⟫) são a frase-ponte que torna a ordem inevitável. Decore a
espinha; os ramos (P = princípios, T = técnicas) penduram nela.

Espinha mínima:
`custo → push left → falha≠bug → fundamentos → 5 fases → DevSecOps → shift L/R → pipeline`

```
                        ┌─────────────┐
                        │  SDLC-SEC   │
                        └──────┬──────┘
                               ▼
                    CUSTO cresce com o tempo
                    (curva de Boehm, 1981 = fundamento)
                               ▼
                    PUSH LEFT · empurrar p/ esquerda
                    (estratégia, não princípio — Janca 2020)
                               │
                               ├── FALHA ≠ BUG
                               │     falha = desenho → reprojeto
                               │     bug   = código  → patch
                               │
         ⟪ junta: se age cedo em TODO o ciclo, o que vale ANTES de qualquer fase? ⟫
                               ▼
                    FUNDAMENTOS (12 conceitos de base)
       CIA · assume breach · ameaça interna · defense in depth ·
       least privilege · cadeia de suprimentos · sem obscuridade ·
       superfície de ataque · sem segredo no código · zero trust ·
       segurança usável · fatores de autenticação
                               │
              ⟪ junta: fixada a base, qual é o ciclo? ⟫
                               ▼
                         ┌── 5 FASES ──┐
                               │
  1 REQUISITOS ── o que garantir
        P: verificável · rastreável · negação explícita
        T: abuse case (McDermott/Fox 99) · misuse (Sindre/Opdahl 00)
           · attack tree (Schneier 99) · bug bar (MS SDL)
        → requisito com teste
                               │
  2 DESIGN ── desenho seguro antes do código  (fase mais barata; nasce a FALHA)
        P: least privilege · defense in depth · fail secure · zero trust
        T: modelagem de ameaças (Shostack 2014, 4 perguntas)
           · DFD (DeMarco 79) · superfície (Manadhata/Wing 04)
        → desenho com mitigações
                               │
  3 CÓDIGO ── código sem falha conhecida
        P: never trust input · nada hard coded · bounds checking
           · erro não ajuda o atacante
        T: inspeção de Fagan (76) · secure code review (OWASP) · SAST no commit
        → código revisado + teste
                               │
  4 TESTES ── falha com evidência   (7 alvos: código·app·infra·BD·APIs·integrações·rede)
        P: evidência · severidade (bug bar) · cobertura dos 7 alvos
        T: SAST · DAST · SCA · fuzzing · secret scanning · pentest
        → achado com severidade + prova + decisão
                               │
  5 DEPLOY+MANUTENÇÃO ── detectar e responder em produção
        P: assume breach · log sem segredo · detecção vale a resposta
        T: resposta a incidentes (NIST 800-61) · gestão de vulnerabilidades
        → incidente contido → vira requisito do próximo ciclo
                               │
  (extra) DESCONTINUAÇÃO ── encerrar sem vazar  (MS SDL/NIST SSDF; só checklist)
                               │
   ⟪ junta: processo escrito ≠ processo executado → como garantir SEMPRE? ⟫
                               ▼
                    DEVSECOPS  (Dev + Sec + Ops)
              responsabilidade compartilhada, automatizada, contínua
                               │
       ├── SHIFT-LEFT (push left por commit)  +  SHIFT-RIGHT (monitorar produção)
       │        previne  ×  detecta — um sem o outro deixa buraco
       │
       ├── PIPELINE: commit→build→testes→análise(SAST/SCA/secrets)→staging→DAST→prod
       │        regra de ouro: verificação falha → pipeline PARA
       │
       ├── FERRAMENTAS: SAST(Semgrep)·DAST(ZAP)·SCA(Trivy)·secrets(gitleaks)
       │                ·fuzzing · WAF/RASP (compram tempo, não corrigem)
       │
       └── SDLC ≠ DevSecOps:  processo (o quê)  ×  prática (como, em escala)
```

Molde repetido de cada fase (decore uma vez, aplique cinco):
**objetivo → princípios → técnicas → saída**.
