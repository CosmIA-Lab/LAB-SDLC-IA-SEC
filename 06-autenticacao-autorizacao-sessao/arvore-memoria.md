# Árvore de memória — Ponto 6

Reconstrução de cabeça de autenticação, autorização e sessão. Cada nó puxa o
próximo; as **juntas** (⟪ ⟫) são a frase-ponte. Decore a espinha; os ramos
(P = princípios, T = técnicas, M = mecanismos) penduram nela.

Espinha mínima:
`3 perguntas (quem é · o que pode · como lembra) → 4 fundamentos → autenticação → sessão → autorização → protocolos (OAuth · OIDC · SAML) → exemplo → erros`

```
                        ┌──────────────────────────────┐
                        │ AUTHN · AUTHZ · SESSÃO         │
                        └──────────────┬───────────────┘
                                       ▼
              3 PERGUNTAS QUE O CÓDIGO CONFUNDE
              AUTENTICAR = quem é (evidência verificada, grau de confiança)
              AUTORIZAR  = o que pode, com este recurso, agora (decisão por acesso)
              SESSÃO     = como lembra a prova (segredo portátil = senha temporária)
              ✗ "logado, logo pode" = confundir 1 com 2 · sessão como detalhe = confundir 3 com nada
                                       │
           ⟪ junta: antes do desenho, o que ele pressupõe? ⟫
                                       ▼
                    FUNDAMENTOS (4)
        IDENTIDADE É AFIRMAÇÃO VERIFICADA, não dado    NIST 800-63: IAL · AAL1/2/3 · FAL — nível proporcional ao risco
        TODO SEGREDO TEM CICLO DE VIDA                 criar · transportar · armazenar · usar · rotacionar · revogar — cada fase, um ataque
        AUTHZ = DECISÃO sobre SUJEITO·AÇÃO·RECURSO·CONTEXTO   PDP decide, PEP aplica, nega por padrão
        CONFIANÇA DELEGADA EXIGE PROTOCOLO             senha do usuário nunca vai ao cliente → OAuth; reinventar = reencontrar os bugs
                                       │
           ⟪ junta: o desenho começa pela prova ⟫
                                       ▼
                    AUTENTICAÇÃO
        P: garantia proporcional · fatores de naturezas distintas · segredo não recuperável · fator de trabalho
           · resistência a phishing · recuperação tão forte quanto o login
        SENHA   criar: 800-63B 17 — sem composição, sem troca periódica, ≥8, lista de vazadas (Adams/Sasse 99)
                armazenar: hash unidirecional + salt (Morris/Thompson 79) → LENTO: bcrypt 99 · scrypt 09 · Argon2 15 · pepper
                           ✗ MD5/SHA + salt = bilhões/s em GPU
                usar: limite de tentativas · tempo constante · erro que não enumera
                ataques: credential stuffing (reuso) · phishing (nenhuma política de senha resolve)
        MFA     sabe · tem · é — duas NATUREZAS
                HOTP RFC 4226 · TOTP RFC 6238 (30 s; phishing em tempo real)
                SMS = "restrito" (SIM swap, SS7) · push = fadiga de MFA (Uber 22) → number matching
                FIDO2/WebAuthn 19 · passkeys 22: par de chaves por sítio, assinatura inclui origem → RESISTENTE A PHISHING (CISA 22)
                STEP-UP: nível da operação > nível da sessão → pede fator agora; acr na sessão
        RECUPERAÇÃO = autenticação com outra evidência; elo mais fraco → link único curto · 2º fator · invalida sessões · notifica
                                       │
           ⟪ junta: a prova foi feita; como lembrar sem que lembrar vire a falha? ⟫
                                       ▼
                    SESSÃO
        P: id vale como senha · imprevisível (≥64 bits, CSPRNG, sem significado) · rotação em mudança de estado
           · expiração e revogação no SERVIDOR · escopo mínimo de envio
        COM ESTADO (cookie)  __Host- · Secure · HttpOnly · SameSite=Lax/Strict · Path/Domain restritos · estado em Redis
                FIXAÇÃO: id antes do login mantido depois → NOVO ID NO LOGIN, sempre
                expiração absoluta (12 h) + inatividade (15–30 min, ASVS V3) · logout e troca de senha invalidam no servidor
        SEM ESTADO (JWT RFC 7519 / JWS 7515)
                revogação impossível → access CURTO (min) + refresh com estado, ROTACIONADO (RFC 9700)
                alg:none · confusão RS256→HS256 (McLean 15) → verificador FIXA alg e chave
                localStorage = XSS lê → cookie HttpOnly no navegador; Authorization em app/serviço
                corpo codificado, não cifrado · validar iss aud exp nbf
                bearer (RFC 6750) vale p/ quem apresenta → vincular: mTLS RFC 8705 · DPoP RFC 9449
                ✗ JWT não é "mais seguro": é outra troca (escala × revogação)
                                       │
           ⟪ junta: quem é, lembrado; agora o que pode ⟫
                                       ▼
                    AUTORIZAÇÃO
        P: autorizar DEPOIS de autenticar, em toda operação (IEEE CSD #3) · negar por padrão · servidor decide
           · PDP único + política declarativa · OBJETO e FUNÇÃO · menor privilégio + separação de deveres (Clark/Wilson 87)
        MODELOS  DAC (dono decide; Unix, ACL) · MAC (rótulos; Bell-LaPadula 73 confid., Biba 77 integr.; SELinux)
                 RBAC (Ferraiolo/Kuhn 92; Sandhu 96; INCITS 359-04): papéis → permissões; não expressa posse nem contexto; explosão de papéis
                 ABAC (NIST 800-162, 14): atributos de sujeito · recurso · ação · ambiente — expressa posse e contexto
                 ReBAC (Zanzibar, Google 19; SpiceDB, OpenFGA): relações entre objetos
                 prática: papel p/ o grosso, atributo/relação p/ o fino → motor (OPA/Rego · Cedar · Casbin), política versionada c/ testes
        OBJETO E FUNÇÃO  OWASP API Top 10 23: #1 BOLA (ação ok, objeto não é dele) · #5 BFLA (rota admin atende qualquer um)
                 regra: carrega o recurso → PDP(sujeito, ação, recurso) · consulta já filtra pelo dono · rota sem política = 404
        CAMADAS  gateway (escopo, papel grosso) → serviço (objeto) → banco (row-level security)
                                       │
           ⟪ junta: identidade de fora, ou agir em nome de outro: protocolos ⟫
                                       ▼
                    PROTOCOLOS
        OAUTH 2.0 (RFC 6749, 12) — DELEGAÇÃO, não autenticação
                papéis: dono do recurso · cliente · servidor de autorização · servidor de recursos → access token c/ escopo e prazo
                fluxo: CÓDIGO DE AUTORIZAÇÃO + PKCE (RFC 7636) — obrigatório p/ todo cliente (RFC 9700, 25)
                máquina: client credentials · device (RFC 8628)
                descontinuados: implicit (token na URL) · password (senha ao cliente)
                ataques: redirect manipulado → URL exata · CSRF no retorno → state (+PKCE) · mix-up → iss (RFC 9207) · bearer roubado → curto, rotação, DPoP/mTLS
        OIDC Core 1.0 (14) — AUTENTICAÇÃO federada sobre OAuth: ID TOKEN (sub iss aud exp auth_time acr) + NONCE · UserInfo · descoberta
        SAML 2.0 (OASIS 05) — federação corporativa em XML: asserção assinada · XML Signature Wrapping (Somorovsky 12) · XXE
        federação: IdP comprometido = tudo comprometido → MFA resistente a phishing p/ admins · HSM · validar asserção INTEIRA
                                       │
           ⟪ junta: o mesmo sistema, redesenhado ⟫
                                       ▼
                    EXEMPLO — API de pagamentos
        authn: passkey 1ª opção · senha + lista de vazadas + Argon2id + limite · AAL2 · step-up TOTP/passkey p/ transferir · sem SMS
        sessão web: __Host-sessao Secure HttpOnly SameSite=Lax · 128 bits · rotação no login e step-up · 12 h / 20 min · JWT sai do localStorage
        app móvel: código + PKCE · access 10 min (aud) · refresh c/ estado rotacionado · DPoP
        parceiro: client credentials + mTLS · escopo antifraude:consultar · 5 min
        authz: PEP gateway (assinatura iss aud exp escopo acr) → PEP serviço → PDP Cedar: dono == sujeito; transferir exige acr≥2 acima do limite; estornar ≠ quem lançou · RLS
        operadores: OIDC corporativo · acr de MFA resistente a phishing
        log: login · falha · step-up · troca · revogação · negado — sujeito e tempo, sem segredo
                                       │
                                       ▼
                    ERROS A NÃO COMETER
        autenticado ≠ autorizado · JWT ≠ "mais seguro" · localStorage · OAuth ≠ authn · SMS ≠ suficiente
        MD5+salt · composição/troca periódica · esconder rota admin · RBAC ≠ posse · logout ≠ apagar cookie
```

Molde repetido de cada bloco (decore uma vez, aplique quatro):
**princípios → mecanismo com estado / sem estado → ataques nomeados → defesa com RFC ou fonte**.
