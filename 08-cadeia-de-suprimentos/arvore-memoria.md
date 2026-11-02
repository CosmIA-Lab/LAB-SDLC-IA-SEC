# Árvore de memória — Ponto 8

Reconstrução de cabeça da segurança da cadeia de suprimentos de software. Cada
nó puxa o próximo; as **juntas** (⟪ ⟫) são a frase-ponte. Decore a espinha; os
ramos (P = princípios, T = técnicas, M = mecanismos) penduram nela.

Espinha mínima:
`código que não é seu → 4 fundamentos → a cadeia e as ameaças A–H → por elo (fonte · deps · build · distribuição · consumo) → princípios → arcabouços (SLSA · in-toto · Sigstore · TUF · reprodutível · SBOM/VEX · SSDF · Scorecard) → exemplo → erros`

```
                        ┌──────────────────────────────┐
                        │ CADEIA DE SUPRIMENTOS         │
                        └──────────────┬───────────────┘
                                       ▼
              CÓDIGO QUE VOCÊ NÃO ESCREVEU
              70–90% do artefato é de terceiros (OSSRA) · + compilador, CI, registro, imagem base, actions
              Thompson 84 "Trusting Trust": não dá para confiar no que não se escreveu → tornar a confiança VERIFICÁVEL
              por que agora: SolarWinds 20 · Codecov 21 · Log4Shell 21 · xz 24 → EO 14028 (21) → SSDF · SLSA · SBOM
                                       │
           ⟪ junta: antes das técnicas, o que elas pressupõem ⟫
                                       ▼
                    FUNDAMENTOS (4)
        CÓDIGO ALHEIO É SEU RISCO                → inventário legível por máquina (SBOM); "não sabia" não é defesa
        CONFIANÇA VERIFICÁVEL, NÃO ASSUMIDA       → proveniência assinada · verificação · reconstrução
        MUITOS ELOS, ATACANTE ESCOLHE O MAIS FRACO → controle por elo; proteger só a dependência deixa o build aberto
        INTEGRIDADE = PROVA CRIPTOGRÁFICA + POLÍTICA → hash sem quem assinou = nada; assinatura sem exigência = decoração
                                       │
           ⟪ junta: o mapa da cadeia ⟫
                                       ▼
        dev →A→ FONTE →B→ BUILD →C,D→ ARTEFATO →F→ REGISTRO →G→ CONSUMIDOR →H→ PRODUÇÃO
                              ↑E (dependências: a mesma cadeia, alheia)                 (SLSA v1.0 A–H)
        SolarWinds = D · Codecov = D + exfiltração · xz = A + E · typosquatting/confusion = H · Log4Shell = E SEM intenção (→ SCA, ponto 7)
                                       │
                                       ▼
                    POR ELO — ameaça (incidente) → controle
        FONTE        commit sem 2FA · sem revisão · mantenedor malicioso (xz 24; event-stream 18)
                     → 2FA FIDO2 · branch protection (revisão, sem push direto) · commits assinados (gitsign) · 2 mantenedores · Scorecard
        DEPENDÊNCIAS typosquatting · dependency confusion (Birsan 21) · mantenedor comprometido (ua-parser-js 21; colors 22) · postinstall · cadeia profunda
                     → lockfile c/ hash (npm ci, --require-hashes) · registro privado/proxy + escopos reservados · revisão de dep nova
                       · ignore-scripts · Renovate c/ QUARENTENA · SCA (ponto 7) · MINIMIZAR
        BUILD        CI comprometido (Codecov 21) · fonte substituída no build (SolarWinds 20 SUNSPOT) · action por tag móvel · segredo fixo · não hermético
                     → runner EFÊMERO · HERMÉTICO (sem rede durante) · OIDC curto · actions por SHA · assinatura em job separado
                       · PROVENIÊNCIA gerada pelo builder (SLSA L3) · reprodutível
        DISTRIBUIÇÃO artefato alterado · registro comprometido · chave roubada · rollback
                     → Sigstore (cosign + Fulcio + Rekor) · proveniência junto · TUF (4 papéis, raiz offline, prazo) · Trusted Publishers · promoção por digest
        CONSUMO      latest · cluster aceita tudo · verificação desligada · SBOM nunca lido
                     → POLÍTICA DE ADMISSÃO (Kyverno/Gatekeeper/policy-controller) · digest · hash na instalação · SBOM consultável + VEX · base mínima
        3 INCIDENTES: SolarWinds (D: hermético + proveniência + reprodutível + assinatura separada teriam agido; SCA não)
                      Codecov (D: hash do script + OIDC curto + menor privilégio + egress restrito)
                      xz (A+E: 2 mantenedores + tarball reprodutível + revisão de binários + quarentena; SCA não)
        PROVENIÊNCIA SLSA = sujeito (hash) · builder (quem assina) · invocação (repo, commit, workflow) · materiais (hashes) → política: "só aceito builder X de repo Y branch main"
        CONTÊINER = cadeia empacotada: base por digest · distroless · multi-stage · scanner (SCA de imagem) · cosign + SBOM como referrers
                                       │
           ⟪ junta: regras que atravessam todos os elos ⟫
                                       ▼
                    PRINCÍPIOS (10)
        inventário legível por máquina (EO 14028; NTIA 21) · verificar, não confiar (Thompson) · fixar e atualizar (SSDF PW.4)
        menor privilégio no pipeline (S&S 75) · build hermético e efêmero · proveniência por quem constrói (in-toto)
        separação de deveres (Clark/Wilson 87) · minimizar · reprodutibilidade quando possível (Lamb/Zacchiroli 21) · responder como processo (SSDF RV)
                                       │
           ⟪ junta: arcabouços com nome ⟫
                                       ▼
                    ARCABOUÇOS E TÉCNICAS
        SLSA v1.0 (OpenSSF 23)   Build L0 nada · L1 proveniência existe · L2 gerada pelo serviço e assinada · L3 build endurecido, proveniência infalsificável
        IN-TOTO (Torres-Arias 19) atestação assinada por passo (quem · entradas · saídas) + layout do dono; formato do SLSA/Sigstore
        SIGSTORE (Newman 22)     Fulcio (cert de minutos p/ identidade OIDC) · Rekor (transparência append-only) · cosign (assina/verifica) · npm/PyPI provenance 23
        TUF (Samuel 10)          raiz offline · alvos · snapshot · timestamp → resiste a rollback, freeze, mix-and-match, chave roubada · Uptane
        REPRODUTÍVEL (Lamb/Zacchiroli 21; Debian 13) mesmo fonte → mesmo bit; terceiro reconstrói e compara → resposta a Thompson · Bazel/Nix
        SBOM + VEX               SPDX (ISO 5962) · CycloneDX (OWASP) · NTIA mínimos 21 · VEX (CISA 22): afetado/não/corrigido + motivo · Dependency-Track
        SSDF (NIST 800-218, 22)  PO · PS (proteger código e artefatos, proveniência) · PW (PW.4 reutilizar seguro, PW.6 build) · RV · autoatestação CISA 23
        SCORECARD (OpenSSF 20)   nota 0–10 por prática do repositório → decidir dependência nova e medir o próprio projeto
        HIGIENE MÍNIMA (CIS 22)  actions por hash · permissions mínimas · OIDC · runner efêmero · sem segredo em PR de fork · lockfile · ignore-scripts · proxy · digest · segredos · assinatura · SBOM
        90 DIAS: mês 1 visibilidade (lockfile, SBOM, Scorecard, inventário) → mês 2 fixação e privilégio → mês 3 proveniência e política (auditoria → bloqueio)
        O PRÓPRIO AMBIENTE: extensões de IDE · curl|bash · agentes de IA e slopsquatting · modelos em pickle → mesmas regras (inventário, hash, menor privilégio, safetensors)
        GOVERNANÇA: NIST 800-161 r1 (C-SCRM) · política de dependências · inventário de fornecedores · plano de resposta · OSS como fornecedor (xkcd 2347; Alpha-Omega; CRA 24)
                                       │
           ⟪ junta: o mesmo sistema, com a cadeia endurecida ⟫
                                       ▼
                    EXEMPLO — API de pagamentos
        fonte: FIDO2 · main protegida · gitsign · segredos no push · dep nova em PR próprio c/ Scorecard
        deps: lockfiles · npm ci / --frozen / go mod verify · ignore-scripts · Artifactory proxy + @pagamentos/ · Renovate 5 dias · SCA alcançabilidade · 1400→800
        build: runners efêmeros · actions por SHA · permissions read-all · OIDC · hermético (Bazel) · assinatura em job separado · SLSA L3
        distribuição: cosign + Rekor · SBOM Syft como referrer · promoção por digest · SDK com Trusted Publisher
        consumo: policy-controller exige assinatura + L3 · digest · distroless · Dependency-Track diário · VEX
        resposta: próximo Log4Shell → minutos para saber onde · SLA 48 h · Renovate já abriu o PR · a imagem antiga não volta
                                       │
                                       ▼
                    ERROS A NÃO COMETER
        SCA ≠ cadeia · SBOM ≠ segurança · assinatura decorativa · fixar versão ≠ hash · Log4Shell ≠ ataque à cadeia
        aberto ≠ mais arriscado · CI interno ≠ confiável · reprodutibilidade ≠ perfeccionismo · Dependabot aberto ≠ dívida
```

Molde repetido de cada elo (decore uma vez, aplique cinco):
**ameaça (letra SLSA + incidente) → controle do elo → arcabouço que o nomeia → mecanismo**.
