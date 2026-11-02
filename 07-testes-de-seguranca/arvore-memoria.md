# Árvore de memória — Ponto 7

Reconstrução de cabeça dos testes de segurança. Cada nó puxa o próximo; as
**juntas** (⟪ ⟫) são a frase-ponte. Decore a espinha; os ramos (P = princípios,
T = técnicas, M = mecanismos) penduram nela.

Espinha mínima:
`o que um teste prova → 4 fundamentos → janelas no ciclo → SAST → DAST/IAST/intrusão → SCA → fuzzing → testes escritos + programa → exemplo → erros`

```
                        ┌──────────────────────────────┐
                        │ TESTES DE SEGURANÇA           │
                        └──────────────┬───────────────┘
                                       ▼
              O QUE UM TESTE PROVA
              presença, não ausência (Dijkstra 70) · requisito NEGATIVO exige caso de ABUSO (McDermott/Fox 99)
              cada técnica vê por UMA JANELA · exemplo: 5 técnicas, 5 achados diferentes, nenhum redundante
                                       │
           ⟪ junta: antes das técnicas, o que elas pressupõem ⟫
                                       ▼
                    FUNDAMENTOS (4)
        TESTE MOSTRA PRESENÇA, NÃO AUSÊNCIA   → cobertura DECLARADA; "SAST limpo" = padrões conhecidos não achados
        REQUISITO NEGATIVO → TESTE NEGATIVO    → ameaça (ponto 2) → requisito → teste que espera 403
        CADA TÉCNICA, UMA JANELA               → branca/cinza/preta × estático/dinâmico × manual/automático; combinar
        ACHADO SEM TRIAGEM É RUÍDO             → Johnson 13: falso positivo mata a ferramenta; dono + prazo + decisão
                                       │
           ⟪ junta: onde cada janela entra no ciclo ⟫
                                       ▼
        pré-commit (seg): lint · segredos → PR (min): SAST diff · SCA · IaC → build: sanitizadores · abuso · checksec
        → noturno (h): SAST completo · DAST autenticado · fuzz contínuo → release: intrusão · arquitetura → prod: bounty · ponto 10
        cada falha pega pela técnica MAIS BARATA capaz (Boehm 81)
                                       │
                                       ▼
                    SAST (código, estático, caixa branca)           Chess/West 07
        como: léxico (Flawfinder) → AST (Semgrep) → TAINT fonte→destino sem neutralizador (Livshits/Lam 05; CodeQL 16)
              → interpretação abstrata (Cousot 77; Infer) → execução simbólica (King 76; KLEE 08)
        vê: injeção · API perigosa · memória por padrão · segredo · TODO o código, com linha
        não vê: config · authz · dependência · runtime · reflexão · falsos positivos 30–70%
        anatomia do falso positivo: framework não modelado · neutralizador caseiro · caminho dependente de valor
        medir antes: precisão × revocação · Juliet (NIST SAMATE) · OWASP Benchmark · 2 semanas em observação
        P: diferencial no PR, completo no noturno · linha de base + dívida · regra ajustada · supressão com motivo · no fluxo do dev
        M: Semgrep · CodeQL · Fortify · Brakeman · gosec · Bandit · ESLint-security
                                       │
                                       ▼
                    DINÂMICO
        DAST (caixa preta, em execução)   rastreia + envia cargas + lê resposta · ZAP · Burp · Nuclei · OpenAPI p/ API
             vê: XSS refletido · SQLi por erro/tempo · cabeçalhos · config · sistema como o atacante vê
             não vê: linha · estados profundos · XSS armazenado · AUTHZ (precisa 2 identidades) · lógica · lento · destrutivo
             P: autenticado · ambiente de teste · passivo no PR, ativo no noturno
        IAST agente no runtime: linha + caminho real, poucos FP; só o que os testes exercitam · RASP = controle, não teste
        INTRUSÃO (manual, caixa cinza)   NIST 800-115 08: planejar · descobrir · atacar · relatar · WSTG 4.2 roteiro · PTES 14
             vê: authz · lógica · encadeamento · o que o modelo marcou   custo: pouca cobertura/hora → modelo diz onde cavar
             P: escopo escrito · dados sintéticos · reprodução + impacto + CVSS como vocabulário · reteste · por release/anual
        BUG BOUNTY = intrusão contínua paga por achado; só depois de tirar o óbvio; exige triagem + divulgação (ponto 10)
        APIs: contrato OpenAPI como roteiro · matriz de authz gerada (tenant, escopo, aud, expirado, sem token, X-User-Id)
              · sequência e estado · fila como fonte de taint · infra: Prowler, kube-bench (CIS), red team c/ ATT&CK (catálogo)
                                       │
                                       ▼
                    SCA (dependências)
        lockfile → árvore c/ versões → NVD/CVE · GitHub Advisory · OSV · licença · SBOM (CycloneDX/SPDX)
        vê: CVE conhecida · licença · abandono   não vê: inédita · pacote MALICIOSO (ponto 8) · ALCANÇABILIDADE
        Log4Shell 21: ninguém sabia que carregava · transitivas são a maioria · VEX (CISA 22) registra afetado/não
        P: inventário antes · transitivas contam · severidade no contexto · atualizar como rotina (Dependabot/Renovate) · política por severidade
        M: Dependency-Check · Snyk · Trivy · Grype · govulncheck (alcançabilidade) · Syft
                                       │
                                       ▼
                    FUZZING (entrada gerada, execução)          Miller/Fredriksen/So 90 · survey Manès 19
        geração (gramática: Peach) × mutação (corpus) → GUIADO POR COBERTURA (AFL Zalewski 13; libFuzzer; AFL++; Böhme 16 Markov)
        + SANITIZADORES (ASan UBSan MSan) senão só vê travamento · contínuo em escala (OSS-Fuzz 16)
        alvo: parsers · decodificadores · protocolos · cripto na entrada · também gerenciadas (Jazzer, Atheris, go -fuzz): exceção, laço, ReDoS
        além: property-based (QuickCheck 00) = fuzz c/ oráculo · diferencial · API com estado (RESTler 19)
        P: harness pequeno e determinístico · corpus válido e versionado · sanitizadores · falha minimizada vira regressão · contínuo
                                       │
           ⟪ junta: o que não é técnica de descoberta: o teste escrito de propósito e o programa ⟫
                                       ▼
                    TESTES ESCRITOS + PROGRAMA
        casos de abuso como testes (403 esperado) · MATRIZ DE AUTORIZAÇÃO · regressão de toda vuln corrigida · testes de arquitetura (ArchUnit)
        P do programa: rastreabilidade ao modelo · técnica mais barata · gate proporcional · triagem como trabalho · cobertura declarada · métricas (tempo de correção, taxa de escape, FP por regra)
        outras varreduras: segredos (gitleaks) · IaC (Checkov) · imagem (Trivy) · nuvem (Prowler)
        TRIAGEM: confirmar (reproduzir) → classificar (CVSS 3.1/4.0 vocabulário; EPSS; bug bar) → decidir (corrigir · aceitar · FP) → fechar (reteste + regressão)
                                       │
           ⟪ junta: o mesmo sistema, com o pipeline montado ⟫
                                       ▼
                    EXEMPLO — API de pagamentos
        pré-commit: segredos · Brakeman/gosec/Semgrep · CERT formato   PR: SAST diff · SCA · IaC · abuso + matriz · ArchUnit · ASan/UBSan · checksec
        noturno: SAST completo · ZAP autenticado + OpenAPI · AFL++/libFuzzer no ISO 8583 e webhook · RESTler · imagens
        release: intrusão guiada pelo modelo (authz entre contas · transferência · parceiro) · 10 flaws   prod: divulgação · bounty · ponto 10
        5 janelas, 5 achados: SAST concatenação · DAST CSP+XSS · SCA XML transitiva · fuzz comprimento negativo · intrusão IDOR → 5 regressões
                                       │
                                       ▼
                    ERROS A NÃO COMETER
        SAST limpo ≠ seguro · DAST ≠ cobertura total · SCA ≠ cadeia de suprimentos · fuzz ≠ só C · intrusão anual ≠ basta
        mais achados ≠ melhor · cobertura funcional ≠ segurança · WAF/RASP ≠ correção · scanner em produção
```

Molde repetido de cada técnica (decore uma vez, aplique cinco):
**o que é → como funciona → vê bem → não vê → princípios de uso → mecanismos**.
