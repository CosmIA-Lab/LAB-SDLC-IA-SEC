# Ponto 4 — Vulnerabilidades em aplicações web · resumo para releitura

**Em uma frase.** As vulnerabilidades web são consequências previsíveis de quatro fatos do modelo de execução — o servidor recebe o que o cliente quiser, o navegador executa o que o servidor devolver, a identidade viaja num token, e a web é composição sob a política de mesma origem — e cada classe tem técnica nomeada de defesa; o OWASP Top 10 prioriza, o ASVS exige.

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
- **Toda entrada é hostil**: o navegador é opcional; validar no servidor, por lista de permissão.
- **Dado e instrução compartilham o canal**: SQL, HTML, shell são texto; separar por construção (parametrizar, codificar por contexto), nunca filtrar caracteres (IEEE CSD #4).
- **A identidade é um token que viaja**: HTTP sem estado; o cookie de sessão vale como a senha (confidencialidade, integridade, escopo, expiração).
- **A web é composição sob mesma origem** (Netscape 1995; Barth, RFC 6454, 2011): origem = esquema + host + porta; a política isola leitura, não envio — por isso CSRF existe; CORS relaxa.

## Mapa
MERMAID

## Taxonomias — o que cada uma é
OWASP Top 10 (2021): A01 controle de acesso · A02 cripto · A03 injeção (inclui XSS) · A04 design inseguro · A05 configuração · A06 componentes · A07 autenticação · A08 integridade · A09 log · A10 SSRF. Edição 2025: configuração sobe para A02, cadeia de suprimentos para A03, SSRF entra em A01, nova categoria de condições excepcionais (fail-open). O Top 10 é consciência, não padrão de requisitos — o padrão é o ASVS 4.0.3 (~280 requisitos, 14 capítulos, L1–L3); o WSTG é como testar. CWE Top 25 (2024): XSS (CWE-79) em 1º, SQLi (89) em 3º, CSRF (352) em 4º, path traversal (22) em 5º — vocabulário formal.

## Classes — mecanismo, princípio, defesa, prova
| Classe | Como acontece | Princípio violado | Técnica (fonte) | Mecanismo | Prova |
|---|---|---|---|---|---|
| Validação ausente, upload, XXE, desserialização | `../`, ENTITY SYSTEM, objeto nativo do cliente (Frohoff e Lawrence, 2015) | entrada hostil; padrões à prova de falha | lista de permissão na borda (ASVS V5); entidades off; JSON com esquema | esquema; limites; parser configurado; nome gerado | valor fora do formato rejeitado |
| Injeção (SQL, comando, LDAP) | tautologia, union, piggy-backed, cega (Halfond, Viegas e Orso, 2006) | dado ≠ instrução | parametrizar; execve com argumentos; menor privilégio no banco | prepared statements; ORM sem interpolação; conta só-leitura; WAF só como profundidade | `' OR '1'='1` devolve vazio |
| XSS (refletido, armazenado, DOM — Klein, 2005) | dado vira script na origem | navegador executa o que recebe | codificação de saída **por contexto** (OWASP); CSP (Stamm, Sterne e Markham, 2010) | template com escape automático; CSP nonce sem inline; HttpOnly; Trusted Types | `<img onerror>` vira texto |
| CSRF | navegador anexa cookie a toda requisição; mesma origem não bloqueia envio | token viaja sem escopo | token sincronizador + Origin (Barth, Jackson e Mitchell, 2008); SameSite (Lax padrão Chrome 2020) | token do framework; SameSite=Lax/Strict; token em Authorization não sofre | form de outra origem → 403 |
| Controle de acesso quebrado (IDOR/BOLA, função, traversal, CORS) | id trocado; rota escondida | mediação completa; authz após authn | checar por objeto e função no servidor, negar por padrão (ASVS V4) | middleware authz; filtro por dono na query; UUID; CORS por lista | matriz papel × recurso → 403/404 |
| Autenticação e sessão | stuffing, brute force, fixação, sem expiração | token viaja; fator de trabalho | NIST SP 800-63B; rotação no login; derivação lenta | Argon2id; limitador; Secure/HttpOnly/SameSite; MFA | fixação falha; 100 tentativas bloqueiam |
| SSRF | servidor busca URL do usuário → 169.254.169.254 (Capital One, 2019) | entrada hostil; menor privilégio de rede | lista de destinos pós-DNS; egress restrito; IMDSv2 | cliente HTTP configurado; proxy de saída | metadados rejeitado antes de conectar |
| Configuração e cabeçalhos | padrão de fábrica, erro verboso, cabeçalho ausente | seguro por padrão | hardening; HSTS (RFC 6797); CSP; nosniff; frame-ancestors | IaC revisada; TLS 1.2+ | varredura de cabeçalhos |
| Cripto e dado sensível | MD5, chave no repo, dado em URL/log | desenho aberto; classificar dado | classificação → controle; primitivas padrão | KMS; Argon2id; tokenização; mascaramento | segredo não recuperável |
| Integridade e componentes | dependência com CVE; script de terceiro | terceiros mudam a superfície | SCA + SBOM; SRI; assinatura (ponto 8) | lockfile; integrity; CSP de script | hash alterado não carrega |
| Log ausente | ataque invisível | registro de comprometimento | eventos com sujeito e tempo; log central só-anexa | logger estruturado; alertas | — |

## A plataforma do navegador
Mesma origem (isola leitura) · CORS (relaxa) · CSP (origens por tipo; proíbe inline) · cookies Secure/HttpOnly/SameSite · HSTS · X-Content-Type-Options nosniff · X-Frame-Options / frame-ancestors (clickjacking) · Subresource Integrity · Trusted Types. Sem declarar, a plataforma roda em modo aberto.

## No exemplo da API de pagamentos
- Busca de beneficiários → SQLi → parâmetro vinculado + conta só-leitura.
- Nome do beneficiário na confirmação → XSS armazenado → escape por contexto + CSP nonce + HttpOnly.
- Formulário de transferência → CSRF → token + SameSite=Lax + Origin.
- `/contas/{id}/saldo` → IDOR → middleware + filtro por dono + UUID.
- Webhook → SSRF → host público pós-DNS, sem redirect, egress fechado.
- Camadas se somam: HttpOnly limita XSS; CSP limita o resto; token + SameSite limitam CSRF.

## Erros a não cometer
- Top 10 não é padrão (ASVS é).
- Validar entrada não resolve XSS: é defeito de saída, por contexto.
- Escapar aspas não é parametrizar.
- WAF não corrige; é camada e ganho de tempo.
- SameSite não acabou com CSRF (Lax envia em GET de navegação).
- Mesma origem não impede CSRF: controla leitura, não envio.

## Três referências para citar
- OWASP Top 10 (2021; 2025) e ASVS 4.0.3 — consciência e padrão.
- Barth, Jackson e Mitchell (2008), *Robust Defenses for CSRF*; Stamm, Sterne e Markham (2010), CSP.
- Halfond, Viegas e Orso (2006), classificação de injeção de SQL; Barth, RFC 6454 (2011), origem.

**Ponte.** A injeção web é o caso de entrada não validada que o ponto 5 vê no nível do código e da memória.
