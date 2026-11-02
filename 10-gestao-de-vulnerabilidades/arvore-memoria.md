# Árvore de memória — Ponto 10

Reconstrução de cabeça da gestão de vulnerabilidades. Cada nó puxa o próximo;
as **juntas** (⟪ ⟫) são a frase-ponte. Decore a espinha; os ramos
(P = princípios, T = técnicas, M = mecanismos) penduram nela.

Espinha mínima:
`janela de exposição → 4 fundamentos → vocabulário → ciclo (descobrir · priorizar · corrigir · verificar · relatar) → divulgação coordenada → ponte com incidente → exemplo → erros`

```
                        ┌──────────────────────────────┐
                        │ GESTÃO DE VULNERABILIDADES    │
                        └──────────────┬───────────────┘
                                       ▼
              A JANELA DE EXPOSIÇÃO
              ciclo de vida: nascimento → descoberta → divulgação → correção publicada → exploit → morte (Arbaugh/Fithen/McHugh 00)
              zero-days exploradas ~10 meses antes da divulgação (Bilge/Dumitraș 12) · tempo até exploração caiu para DIAS (Mandiant 23–24)
              DBIR 24: exploração de vuln como vetor inicial ~3× · o problema é a N-DAY, não a zero-day
              >28 mil CVEs em 23, >40 mil em 24 · >50% "alta/crítica" · <5% exploradas → NÃO DÁ PARA CORRIGIR TUDO
                                       │
           ⟪ junta: antes do processo, o que ele pressupõe ⟫
                                       ▼
                    FUNDAMENTOS (4)
        VULNERABILIDADE É CERTA; A VARIÁVEL É O TEMPO   → medir e reduzir o TEMPO DE EXPOSIÇÃO, não prometer zero
        NÃO SE GERENCIA O QUE NÃO SE CONHECE            → inventário em 3 camadas (ativos · software · SBOM), gerado automaticamente, com dono
        NOTA NÃO É RISCO                                 → Spring et al. 21: CVSS base ignora contexto e exploração · risco = probabilidade × impacto AQUI
        PROCESSO CONTÍNUO COM CICLO E MÉTRICA            → NIST 800-40 r4 (22): patching é gestão de risco · BOD 19-02: 15/30 dias · BOD 22-01: KEV
                                       │
           ⟪ junta: o vocabulário sem o qual o processo não se descreve ⟫
                                       ▼
                    VOCABULÁRIO
        CVE (MITRE 99, CNAs) = identificador · NVD (NIST) = enriquecimento · CWE = classe · CPE = produto (purl é melhor)
        CVSS (FIRST) 3.1 (19) / 4.0 (23): base (AV AC PR UI S C I A) · temporal/ameaça · ambiental → ler o VETOR, não só a nota
        KEV (CISA 21, BOD 22-01) = exploração confirmada → "corrija agora" · EPSS (FIRST 21) = probabilidade em 30 dias
        SSVC (SEI 19; CISA 21) = ÁRVORE DE DECISÃO: exploração (none/PoC/active) · automatizável · impacto técnico · missão → Track · Track* · Attend · ACT
        VEX (CISA 22) = afetado/não/corrigido/investigando · CSAF (OASIS 22) = aviso legível por máquina · zero-day ≠ maioria
        3 FALHAS DE GESTÃO: Equifax 17 (Struts, 2 meses, inventário falhou) · WannaCry 17 (MS17-010, 2 meses) · MOVEit 23 (zero-day: inventário + mitigação + detecção)
                                       │
           ⟪ junta: o processo, etapa por etapa ⟫
                                       ▼
                    O CICLO
        DESCOBRIR   varredura c/ CREDENCIAL (Nessus/Qualys/OpenVAS) · agente · imagens (Trivy) · SBOM × CVE contínuo (Dependency-Track) · CSPM · testes (ponto 7) · CSAF · relatos externos · KEV
                    P: contínua/semanal · cobertura medida · dedup · dono na descoberta
        PRIORIZAR   exploração (KEV, EPSS, exploit, logs) + exposição (internet? alcançável? VEX) + impacto (o que o ativo faz) + vetor CVSS → CLASSE → PRAZO
                    SSVC em detalhe: 4 pontos de decisão → Act/Attend/Track; mesma CVE, 2 decisões (decodificador Act; relatórios Track + VEX)
                    prazos exemplo: KEV/crítica exposta 48 h–7 d · crítica interna/alta exposta 15 d · alta interna/média exposta 30 d · resto 90 d
                    EXCEÇÃO: dono · motivo · mitigação · PRAZO DE REVISÃO · visibilidade — sem prazo = aceitação permanente
        CORRIGIR    patch · atualizar · reconfigurar · substituir · MITIGAR enquanto não chega (virtual patching WAF/RASP, isolamento) — mitigação não fecha
                    NIST 800-40 r4: teste · ANÉIS · reversão · janela + caminho de emergência · automação · imutável: RECONSTRUIR, não remendar
                    por ambiente: servidores (anéis) · estações (auto) · contêineres (imagem nova + admissão) · nuvem gerenciada (responsabilidade compartilhada)
                                 · dependências (SBOM → VEX → Renovate → pipeline) · firmware/OT (segmentar + aceitar c/ revisão) · terceiros (CSAF)
                    NUVEM: config é vuln sem CVE → CSPM (Prowler) vs CIS · corrigir em código · drift
        VERIFICAR   nova varredura · SBOM novo · regressão (ponto 7) · regra de mitigação testada → item fecha COM EVIDÊNCIA
        RELATAR     tempo de exposição por classe (mediana, p90) · conformidade c/ prazo · idade dos abertos · cobertura · KEV abertos = 0 · exceções · reincidência (→ ponto 1)
                    ✗ total de achados · total corrigido · nota média
        PROGRAMA    papéis: dono do ativo · segurança · PSIRT · gestão (exceções) · política escrita · conformidade como PISO (PCI DSS 4.0 6.3/11.3: 1 mês; ISO 27001 A.8.8; BOD 22-01; CRA)
                    maturidade (SAMM, BSIMM): "o tempo de exposição é medido, e alguém olha?"
        CADÊNCIA    dia: KEV + avisos + act · semana: triagem 1 h + janela · mês: métricas + varredura completa · trimestre: exceções + inventário + exercício de mesa · ano: VDP
                                       │
           ⟪ junta: a porta de entrada do mundo externo ⟫
                                       ▼
                    DIVULGAÇÃO COORDENADA (CVD)
        ISO/IEC 29147 (18) = receber e publicar · ISO/IEC 30111 (19) = tratar por dentro · CERT/CC Guide (Householder 17) = papéis e problemas · FIRST PSIRT Framework
        debate: full → "responsável" → COORDENADA (prazo acordado, publicação conjunta) · Project Zero 90 dias · CISA 45 · explorada → dias
        preparar: security.txt (RFC 9116, 22) · VDP com escopo + SAFE HARBOR (BOD 20-01) · PSIRT (48 h para confirmar) · aviso CSAF + CVE (CNA própria) + VEX + crédito · bounty só depois do óbvio
        como consumidor: assinar CSAF/feeds · inventário diz se afeta · VEX do fornecedor reduz trabalho
                                       │
           ⟪ junta: quando a correção não chegou a tempo ⟫
                                       ▼
                    PONTE COM INCIDENTE — vulnerabilidade = CONDIÇÃO · incidente = EVENTO
        NIST 800-61 r2 (12) / r3 (25): preparação · detecção e análise · contenção · erradicação · recuperação · pós-incidente
        detecção vem dos logs dos pontos 4, 6, 9 (WAF, Falco, authn anômala) · contenção = mitigação pronta · pós-incidente → REQUISITO do próximo ciclo (10 → 1)
                                       │
           ⟪ junta: o mesmo sistema, no dia da CVE ⟫
                                       ▼
                    EXEMPLO — a segunda-feira da CVE
        9h00 Dependency-Track abre: XML crítica em 2 imagens, dono automático · 9h20 SSVC: decodificador ACT 48 h (PoC, mTLS parceiro, toca dinheiro) · relatórios TRACK + VEX (não alcançável)
        10h00 mitigação: regra no gateway contra ENTITY + alerta · Falco · logs · 10h30 Renovate já abriu PR → pipeline (ponto 7) → build assinado (ponto 8) → homolog → prod em anel → admissão recusa a antiga
        14h00 verificação: nenhuma imagem com a versão · regressão · fecha com evidência → EXPOSIÇÃO: 5 h
        quinta: pesquisador via security.txt: IDOR na v1 que o gateway ainda roteava → confirma em 24 h · desliga rota · aviso · crédito · bounty → lição: catálogo gerado do gateway (9) + teste rota fora → 404 (7)
        se explorada: Falco 9h15 → incidente: isolar pod · revogar Vault · bloquear parceiro → erradicar · recuperar → lição: decodificador em C → Rust (5)
                                       │
                                       ▼
                    ERROS A NÃO COMETER
        CVSS alto ≠ primeiro · varredura limpa ≠ seguro · tudo imediatamente ≠ sempre (anéis) · zero-day ≠ o problema (n-day é)
        WAF ≠ correção · "responsável" ≠ silêncio · CVE no componente ≠ minha app vulnerável · scanner ≠ gestão · corrigir contêiner rodando · exceção aprovada ≠ resolvido
```

Molde repetido de cada etapa (decore uma vez, aplique cinco):
**fontes/entradas → princípios → método com fonte → mecanismo → o que NÃO medir**.
