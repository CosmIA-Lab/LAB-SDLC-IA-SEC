# Ponto 6 — Autenticação, autorização e gestão de sessão · resumo para releitura

**Em uma frase.** Três problemas que o código confunde: provar quem é (autenticação, no nível de garantia do risco — NIST SP 800-63B), decidir o que pode fazer com este recurso agora (autorização por sujeito, ação, recurso e contexto, negando por padrão) e lembrar a prova entre requisições sem que ela vire a vulnerabilidade (sessão) — com protocolos nomeados quando a identidade vem de fora (OIDC, SAML) ou o acesso é em nome de outro (OAuth 2.0).

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
- **Identidade é afirmação verificada, não dado**: NIST SP 800-63 separa IAL, AAL (1, 2, 3) e FAL; o nível é proporcional ao dano.
- **Todo segredo tem ciclo de vida**: criar, transportar, armazenar, usar, rotacionar, revogar — cada fase um ataque.
- **Autorização é decisão por acesso sobre sujeito, ação, recurso e contexto**: PDP decide, PEP aplica, nega por padrão (mediação completa).
- **Confiança delegada exige protocolo**: a credencial do usuário nunca vai ao cliente; reinventar OAuth é reencontrar seus bugs.

## Mapa
MERMAID

## Autenticação
| Tema | Regra | Fonte / mecanismo |
|---|---|---|
| Senha — criar | sem composição, sem troca periódica, ≥ 8, lista de vazadas, medidor | NIST SP 800-63B (2017); Adams e Sasse (1999) |
| Senha — armazenar | hash unidirecional + salt (Morris e Thompson, 1979) → lento: bcrypt (1999), scrypt (2009), Argon2 (2015); pepper | MD5/SHA + salt é rápido demais (GPU) |
| Senha — usar | limite de tentativas; comparação em tempo constante; erro que não enumera | credential stuffing e phishing: política de senha não resolve |
| MFA | duas naturezas: sabe, tem, é | HOTP (RFC 4226), TOTP (RFC 6238; phishing em tempo real); SMS "restrito"; push (fadiga → number matching) |
| Resistência a phishing | assinatura inclui a origem; chave privada não sai do dispositivo | FIDO2/WebAuthn (W3C, 2019); passkeys (2022); CISA (2022) |
| Step-up | operação exige nível maior que a sessão → fator agora; `acr` | AAL por operação |
| Recuperação | é autenticação com outra evidência; elo mais fraco | link único curto; 2º fator; invalida sessões; notifica |
| Outros autenticadores | mTLS (serviço, parceiro); Kerberos (ticket curto; credencial longa não viaja); chave de API (senha sem usuário: hash, escopo, prazo); link mágico (baixo risco); biometria só local | RFC 8446/8705; RFC 4120 |

Cadeia de tomada de conta: reconhecimento → obtenção da credencial → contorno do 2º fator → persistência (troca e-mail, adiciona fator) → uso. "Senha forte" age só no passo 2.

## Sessão
| Princípio | Regra |
|---|---|
| Id vale como senha | nunca em URL, localStorage ou log |
| Imprevisível | CSPRNG, ≥ 64 bits, sem significado |
| Rotação | novo id no login, no step-up, na troca de senha (mata fixação) |
| Expiração e revogação no servidor | absoluta (12 h) + inatividade (15–30 min, ASVS V3); logout invalida |
| Escopo de envio | `__Host-`, Secure, HttpOnly, SameSite=Lax/Strict, Path |

**Com estado (cookie):** revogação trivial; repositório de sessão para escala. **Sem estado (JWT, RFC 7519):** revogação impossível → access curto (minutos) + refresh com estado, rotacionado, família revogada se reusado (RFC 9700); `alg` e chave fixados no verificador (alg:none, RS→HS — McLean, 2015); validar iss, aud, exp, nbf; nada sensível no corpo; bearer vale para quem apresenta → vincular com DPoP (RFC 9449) ou mTLS (RFC 8705). JWT não é "mais seguro": é outra troca. Entre serviços: token exchange (RFC 8693) com `aud` do destino; nunca `X-User-Id` em claro.

## Autorização
| Modelo | Fonte | Expressa | Limite |
|---|---|---|---|
| DAC / MAC | Unix, ACL / Bell-LaPadula (1973), Biba (1977) | dono decide / rótulos centrais | — |
| RBAC | Ferraiolo e Kuhn (1992); Sandhu et al. (1996); INCITS 359 (2004) | papéis → permissões; administração em escala | não expressa posse nem contexto; explosão de papéis |
| ABAC | NIST SP 800-162 (2014) | atributos de sujeito, recurso, ação, ambiente | complexidade da política |
| ReBAC | Zanzibar (Google, 2019); SpiceDB, OpenFGA | relações entre objetos | — |

Prática: papel para o grosso, atributo ou relação para o fino; motor de políticas (OPA/Rego, Cedar, Casbin) como PDP; política versionada com testes. Princípios: autorizar depois de autenticar em toda operação (IEEE CSD #3); negar por padrão; servidor decide; objeto **e** função (OWASP API Top 10: BOLA #1, BFLA #5); menor privilégio e separação de deveres (Clark e Wilson, 1987). Camadas: gateway (escopo) → serviço (objeto) → banco (RLS). Deputado confuso (Hardy, 1988): a autoridade que executa é a do sujeito original, propagada.

## Protocolos
| Protocolo | O que resolve | Peças | Cuidados |
|---|---|---|---|
| OAuth 2.0 (RFC 6749, 2012) | delegação — não autentica | dono do recurso, cliente, servidor de autorização, servidor de recursos; access token com escopo e prazo | código de autorização + PKCE (RFC 7636) obrigatório (RFC 9700, 2025); implicit e password descontinuados; redirect exato; state; iss (RFC 9207); refresh rotacionado |
| OpenID Connect Core (2014) | autenticação federada sobre OAuth | ID token (sub, iss, aud, exp, auth_time, acr) + nonce; UserInfo; descoberta | verificar assinatura com alg fixo, iss, aud, exp, nonce, acr; `sub` é o id estável, não o e-mail |
| SAML 2.0 (OASIS, 2005) | federação corporativa em XML | asserção assinada | XML Signature Wrapping (Somorovsky et al., 2012); XXE; biblioteca madura |

Roteiro de verificação de ID token: assinatura (alg fixo) → iss → aud → exp/iat → nonce → acr → só então sub. Pular (3) aceita token de outro cliente; pular (5) é CSRF no login; ler alg do token é alg:none. O provedor de identidade é o ativo mais valioso: MFA resistente a phishing para admins, chaves em HSM, logout federado, fail secure se cair.

## No exemplo da API de pagamentos
- Login: passkey primeiro; senha + lista de vazadas + Argon2id + limite; AAL2; step-up para transferir; sem SMS.
- Sessão web: `__Host-sessao` Secure HttpOnly SameSite=Lax, 128 bits, rotação no login, 12 h / 20 min; JWT sai do localStorage.
- App móvel: código + PKCE; access 10 min; refresh com estado rotacionado; DPoP.
- Parceiro: client credentials + mTLS; escopo `antifraude:consultar`; 5 min.
- Authz: PEP no gateway (assinatura, iss, aud, exp, escopo, acr) → PEP no serviço → PDP Cedar: dono == sujeito; transferir exige acr ≥ 2 acima do limite; estornar ≠ quem lançou; RLS.

## Erros a não cometer
- Autenticado ≠ autorizado.
- JWT não é mais seguro que sessão com estado.
- Token no localStorage: XSS leva.
- OAuth não autentica; OIDC sim.
- SMS não é segundo fator suficiente; MD5 + salt não protege senha.
- Logout não é apagar o cookie: invalida no servidor.

## Três referências para citar
- NIST SP 800-63B (2017) — níveis AAL, senhas, MFA, recuperação.
- RFC 6749 (OAuth 2.0) + RFC 7636 (PKCE) + RFC 9700 (BCP, 2025); OpenID Connect Core (2014).
- Ferraiolo e Kuhn (1992) RBAC; NIST SP 800-162 (2014) ABAC; Morris e Thompson (1979) senhas.

**Ponte.** O requisito negativo de autorização é o que o teste de segurança prova (ponto 7).
