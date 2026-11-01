# Árvore de memória — Ponto 4

Reconstrução de cabeça das vulnerabilidades em aplicações web. Cada nó puxa o
próximo; as **juntas** (⟪ ⟫) são a frase-ponte que torna a ordem inevitável.
Decore a espinha; os ramos (P = princípios, T = técnicas, M = mecanismos)
penduram nela.

Espinha mínima:
`4 fatos do modelo → caminho da requisição → taxonomias (Top 10 · CWE · ASVS) → classes por estação → plataforma do navegador → exemplo → erros`

```
                        ┌───────────────────────────┐
                        │ VULNERABILIDADES WEB       │
                        └─────────────┬─────────────┘
                                      ▼
              POR QUE A WEB É DIFERENTE — programa dividido por uma fronteira que o atacante controla
                                      │
                    FUNDAMENTOS (4 fatos do modelo)
        1 TODA ENTRADA É HOSTIL          navegador é opcional → validar no servidor, por permissão
        2 DADO E INSTRUÇÃO NO MESMO CANAL SQL/HTML/shell são texto → separar por construção (IEEE CSD 14 #4)
        3 IDENTIDADE É TOKEN QUE VIAJA   HTTP sem estado; sessão = segredo portátil → C·I·escopo·expiração
        4 WEB É COMPOSIÇÃO sob MESMA ORIGEM  (Netscape 95; Barth RFC 6454 11) — isola LEITURA, não ENVIO
                                      │
           ⟪ junta: dezenas de nomes; onde cada um vive? no caminho da requisição ⟫
                                      ▼
        navegador → PARSE/VALIDAÇÃO → AUTHN → AUTHZ → LÓGICA → DADOS/SERVIÇOS → RESPOSTA → navegador
                        │               │        │                 │              │
                 validação · upload   stuffing  IDOR/BOLA        injeção        XSS · cabeçalhos
                 XXE · desserial.     fixação   função · CSRF    SSRF           dado exposto
                                      │
           ⟪ junta: as listas dizem o que é frequente, não o que fazer ⟫
                                      ▼
                    TAXONOMIAS
        OWASP TOP 10 (2021): A01 acesso · A02 cripto · A03 injeção(+XSS) · A04 design · A05 config
                            A06 componentes · A07 authn · A08 integridade · A09 log · A10 SSRF
                    (2025 RC): config→A02 · cadeia de suprimentos→A03 · SSRF dentro de A01 · nova: condições excepcionais
                    = CONSCIÊNCIA e prioridade, NÃO padrão de requisitos
        CWE TOP 25 (2024): 1 XSS-79 · 3 SQLi-89 · 4 CSRF-352 · 5 traversal-22 = vocabulário + evidência
        ASVS 4.0.3 (5.0 em 2025): ~280 requisitos · 14 capítulos · L1/L2/L3 = PADRÃO verificável · WSTG = como testar
                                      │
           ⟪ junta: cada classe = fato ignorado → princípio violado → técnica com fonte → mecanismo → prova ⟫
                                      ▼
                    CLASSES (ordem do caminho)
        VALIDAÇÃO  traversal-22 · XXE · desserialização-502 (Frohoff/Lawrence 15)
                   T: lista de permissão na borda (ASVS V5) · entidades off · sem formato nativo do cliente
                   M: esquema · limites · parser configurado · nome gerado pelo servidor
        INJEÇÃO    SQLi-89 (Halfond/Viegas/Orso 06: tautologia·union·piggy·cega) · comando-78 · LDAP · template
                   P: separar dado de instrução   T: PARAMETRIZAR (não escapar) · execve c/ args · menor privilégio no banco
                   M: prepared statements · ORM sem interpolação · conta só-leitura · WAF = só profundidade
        XSS-79     refletido · armazenado · DOM (Klein 05)   P: navegador executa o que recebe
                   T: CODIFICAÇÃO DE SAÍDA POR CONTEXTO (OWASP) · CSP (Stamm/Sterne/Markham 10) · textContent/Trusted Types
                   M: template c/ escape automático · CSP nonce sem unsafe-inline · HttpOnly · DOMPurify só p/ HTML requisito
                   ✗ é defeito de SAÍDA, não de entrada
        CSRF-352   navegador anexa cookie a toda requisição; mesma origem não bloqueia ENVIO
                   T: token sincronizador + Origin (Barth/Jackson/Mitchell 08) · SameSite (rfc6265bis; Lax padrão Chrome 20)
                   M: token do framework · SameSite=Lax/Strict · Origin na API · token em Authorization não sofre CSRF
        ACESSO     IDOR/BOLA-639 · função sem checagem-862 · elevação · traversal · CORS *   (A01 em 21 e 25)
                   P: mediação completa · authz após authn (IEEE CSD #3)
                   T: checar por OBJETO e por FUNÇÃO no servidor, negar por padrão (ASVS V4) · Check Point (ponto 3)
                   M: middleware authz · filtro por dono na query · OPA/Cedar · UUID · CORS explícito
        AUTHN/SESSÃO  stuffing · brute force · fixação-384 · sequestro · sem expiração-613
                   T: NIST 800-63B (lista de vazadas, sem composição/troca) · ROTACIONAR sessão no login · derivação lenta
                   M: Argon2id · limitador · Secure+HttpOnly+SameSite · expiração · MFA   (ponto 6 aprofunda)
        SSRF-918   servidor busca URL do usuário → rede interna, 169.254.169.254 (Capital One 19)
                   T: lista de destinos PÓS-DNS, sem redirect · egress restrito · IMDSv2
        CONFIG     padrão de fábrica · erro verboso · cabeçalhos ausentes   P: seguro por padrão (Howard/Lipner 06)
                   M: IaC revisada · HSTS (RFC 6797) · CSP · nosniff · frame-ancestors · TLS 1.2+
        CRIPTO     senha em claro/MD5 · chave no repo · cripto própria · dado em URL/log
                   T: classificar → controle · primitivas padrão   M: KMS · Argon2id · tokenização · mascaramento
        INTEGRIDADE/COMPONENTES  dependência c/ CVE · desserialização · pipeline   T: SCA+SBOM · SRI · assinatura (ponto 8)
        LOG        ataque invisível = registro de comprometimento ausente (S&S 75; R do STRIDE)  (ponto 10)
                                      │
           ⟪ junta: há defesas que não são de classe nenhuma: a PLATAFORMA ⟫
                                      ▼
                    O NAVEGADOR OFERECE, A APLICAÇÃO DECLARA
        mesma origem (isola leitura) · CORS (relaxa) · CSP (origens de script) · cookies Secure/HttpOnly/SameSite
        HSTS · nosniff · X-Frame-Options/frame-ancestors (clickjacking) · SRI · Trusted Types
                                      │
           ⟪ junta: tudo na mesma API de pagamentos ⟫
                                      ▼
                    EXEMPLO — classe no lugar onde vive, defesa nomeada, teste
        busca → SQLi → parâmetro + conta só-leitura → ' OR '1'='1 devolve vazio
        nome do beneficiário → XSS → escape por contexto + CSP nonce + HttpOnly → <img onerror> vira texto
        transferência → CSRF → token + SameSite + Origin → form de outra origem = 403
        /contas/{id} → IDOR → middleware do ponto 3 + filtro por dono + UUID → conta do outro = 404
        login → stuffing/fixação → 800-63B + rotação + Argon2id + limite + MFA
        webhook → SSRF → host público pós-DNS, sem redirect, egress fechado → 169.254… rejeitado
        upload → validação → tipo pelo conteúdo, nome gerado, fora da raiz, nosniff
        cabeçalhos → HSTS · CSP · nosniff · frame-ancestors · cookies Secure
        terceiros → SRI + CSP + SCA      log → sujeito, tempo, sem cartão, alerta de 403
        → camadas se somam: HttpOnly limita XSS; CSP limita o resto; token+SameSite limitam CSRF
                                      │
                                      ▼
                    ERROS A NÃO COMETER
        Top 10 ≠ padrão (ASVS é) · validar entrada ≠ resolver XSS · escapar ≠ parametrizar · WAF ≠ correção
        SameSite ≠ fim do CSRF · HTTPS ≠ anti-injeção · mesma origem não impede CSRF · sanitizar ≠ escapar
```

Molde repetido de cada classe (decore uma vez, aplique dez):
**o que é → como acontece → princípio violado → técnica (fonte) → mecanismo → prova**.
