# Ponto 8 — Segurança da cadeia de suprimentos de software · resumo para releitura

**Em uma frase.** Entre 70% e 90% do artefato é código alheio, mais compilador, CI, registro e imagem base; Thompson (1984) mostrou que não se pode confiar em código que não se escreveu por inteiro, e a resposta não é desconfiar de tudo, é tornar a confiança verificável — por elo (fonte, dependências, build, distribuição, consumo), com inventário (SBOM), proveniência assinada (SLSA, in-toto, Sigstore) e política que exige a verificação onde o artefato é consumido.

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
- **Código que você não escreveu é seu risco**: inventário legível por máquina; "não sabia que usava" não é defesa (Log4Shell).
- **Confiança verificável, não assumida**: proveniência, verificação de integridade, reconstrução.
- **Muitos elos; o atacante escolhe o mais fraco**: controle por elo; proteger só a dependência deixa o build aberto.
- **Integridade é prova criptográfica mais política**: hash sem quem assinou não prova nada; assinatura sem exigência no consumo é decoração.

## Mapa
MERMAID

## Ameaças por elo (letras SLSA v1.0) — incidente e controle
| Elo | Ameaças | Incidente | Controles |
|---|---|---|---|
| Fonte | A, B: commit não autorizado; mantenedor malicioso | xz-utils 2024 (dois anos de engenharia social até virar mantenedor); event-stream 2018 | 2FA FIDO2; branch protection (revisão, sem push direto); commits assinados (gitsign); dois mantenedores; Scorecard |
| Dependências | E, H: typosquatting; dependency confusion (Birsan, 2021); mantenedor comprometido (ua-parser-js 2021; colors 2022); postinstall | — | lockfile com hash (`npm ci`, `--require-hashes`); registro privado como proxy + escopos reservados; revisão de dependência nova; `ignore-scripts`; Renovate com quarentena; SCA; minimizar |
| Build | C, D: CI comprometido; fonte substituída durante o build; action por tag móvel; segredo fixo; não hermético | SolarWinds 2020 (SUNSPOT trocava o fonte na compilação; binário saía assinado); Codecov 2021 (script exfiltrava variáveis de ambiente) | runner efêmero; hermético (sem rede durante); OIDC curto; actions por SHA; assinatura em job separado; proveniência pelo builder (SLSA L3); reprodutível |
| Distribuição | F, G: artefato alterado; registro comprometido; chave roubada; rollback | — | Sigstore (cosign + Fulcio + Rekor); proveniência junto; TUF; Trusted Publishers (npm/PyPI, 2023); promoção por digest |
| Consumo | H: `latest`; cluster aceita tudo; SBOM nunca lido | — | política de admissão (Kyverno, Gatekeeper); digest; hash na instalação; SBOM consultável + VEX; base mínima |

Log4Shell (2021) não é ataque à cadeia: é E sem intenção — caso para SCA e SBOM, não para SLSA.

## Princípios
Inventário legível por máquina (EO 14028; NTIA 2021) · verificar, não confiar (Thompson) · fixar e atualizar (SSDF PW.4) · menor privilégio no pipeline · build hermético e efêmero · proveniência gerada por quem constrói (in-toto) · separação de deveres (Clark e Wilson) · minimizar dependências · reprodutibilidade quando possível (Lamb e Zacchiroli, 2021) · responder como processo (SSDF RV).

## Arcabouços e técnicas
| Arcabouço | Fonte | O que é |
|---|---|---|
| SLSA | OpenSSF, v1.0 (2023) | modelo de ameaças A–H; Build L0 nada · L1 proveniência existe · L2 gerada pelo serviço e assinada · L3 build endurecido, proveniência infalsificável |
| in-toto | Torres-Arias et al. (2019) | atestação assinada por passo (quem, entradas, saídas) + layout do dono; formato das atestações SLSA/Sigstore |
| Sigstore | Newman et al. (2022) | Fulcio (certificado de minutos para identidade OIDC) · Rekor (registro de transparência) · cosign (assina e verifica) |
| TUF | Samuel et al. (2010) | quatro papéis (raiz offline, alvos, snapshot, timestamp); resiste a rollback, freeze, chave roubada |
| Builds reprodutíveis | Lamb e Zacchiroli (2021); Debian | mesmo fonte → mesmo bit; terceiro reconstrói e compara — resposta a Thompson |
| SBOM + VEX | SPDX (ISO/IEC 5962); CycloneDX; VEX (CISA, 2022) | inventário por artefato; afetado/não afetado/corrigido com motivo; Dependency-Track |
| SSDF | NIST SP 800-218 (2022) | PO, PS, PW, RV; autoatestação (CISA, 2023) |
| Scorecard | OpenSSF (2020) | nota 0–10 por prática do repositório |
| Higiene mínima | CIS Guide (2022) | actions por hash; permissions mínimas; OIDC; runner efêmero; lockfile; ignore-scripts; proxy; digest; assinatura; SBOM |

Proveniência SLSA contém: sujeito (hash do artefato), builder (quem assina), invocação (repositório, commit, workflow), materiais (hashes das entradas). Política: "só aceito builder X, de repositório Y, branch main". Contêiner é a cadeia empacotada: base por digest, distroless, multi-stage, scanner, cosign + SBOM como referrers. Adoção em 90 dias: visibilidade → fixação e privilégio → proveniência e política. O próprio ambiente de desenvolvimento é cadeia: extensões de IDE, `curl | bash`, agentes de IA (slopsquatting), modelos em pickle.

## No exemplo da API de pagamentos
- Fonte: FIDO2; main protegida; gitsign; dependência nova em PR próprio com Scorecard.
- Dependências: lockfiles; `npm ci`; ignore-scripts; Artifactory como proxy + `@pagamentos/`; Renovate com 5 dias; meta de 1.400 → 800 pacotes.
- Build: runners efêmeros; actions por SHA; OIDC; hermético (Bazel); assinatura em job separado; SLSA L3.
- Distribuição e consumo: cosign + Rekor; SBOM Syft; promoção por digest; policy-controller exige assinatura + L3; Dependency-Track diário; VEX.
- No próximo Log4Shell: minutos para saber onde; PR já aberto; a imagem antiga não volta.

## Erros a não cometer
- SCA não é cadeia de suprimentos.
- SBOM é inventário, não controle.
- Assinatura sem identidade verificável e sem exigência no consumo é decorativa.
- Fixar versão sem hash ainda confia no registro.
- Log4Shell ≠ SolarWinds: dependência vulnerável vs build comprometido.
- CI interno não é confiável: é fronteira.

## Três referências para citar
- SLSA v1.0 (OpenSSF, 2023) — modelo de ameaças e níveis.
- Thompson (1984), *Reflections on Trusting Trust*; Torres-Arias et al. (2019), in-toto; Newman et al. (2022), Sigstore.
- NIST SP 800-218 (2022) SSDF; EO 14028 (2021); Ladisa et al. (2023), taxonomia de ataques.

**Ponte.** A imagem assinada que sai daqui é o que o ponto 9 coloca em execução.
