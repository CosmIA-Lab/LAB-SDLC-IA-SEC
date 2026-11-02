# Pontes — levar as vulnerabilidades web para os outros nove pontos

Este arquivo responde a uma observação de estudo: os dez pontos são tão ligados que o que se domina bem em um serve de âncora nos outros. Aqui a âncora é o ponto 4 — injeção de SQL, XSS, CSRF, IDOR, SSRF — e o mapa mostra, para cada um dos outros nove pontos, *onde* essas vulnerabilidades entram legitimamente como exemplo, caso ou mecanismo, e *qual frase* faz a ponte. Não é truque: é a rastreabilidade do laboratório (ameaça → requisito → controle → teste) vista a partir da vulnerabilidade concreta.

Regra de uso: a vulnerabilidade web entra como **exemplo** (seção de aprofundamento), **caso real** (uma frase) ou **ilustração de um princípio** — nunca como o assunto. O assunto continua sendo o ponto sorteado.

---

## 1. A ideia que atravessa tudo: dado interpretado como instrução

Antes do mapa, a ponte mais forte. Injeção de SQL, XSS, injeção de comando, string de formato em C, injeção de template, XXE, Log4Shell e a falha do Struts que derrubou a Equifax são **a mesma classe de erro**: dado controlado pelo atacante chega a um interpretador que não consegue distinguir onde o dado termina e a instrução começa. O IEEE Center for Secure Design (2014) a nomeia como a quarta falha de design: "separe estritamente dado de instrução de controle". Quem domina XSS e SQLi domina a classe; o resto é reconhecê-la em outro interpretador.

| Interpretador | Vulnerabilidade | Ponto onde aparece | Defesa por construção |
|---|---|---|---|
| SQL | injeção de SQL (CWE-89) | 4, 5, 7, 10 | consulta parametrizada |
| Navegador (HTML/JS) | XSS (CWE-79) | 4, 8, 9 | codificação de saída por contexto + CSP |
| Shell | injeção de comando (CWE-78) | 5 | vetor de argumentos, sem shell |
| `printf` em C | string de formato (CWE-134) | 5 | `printf("%s", x)` |
| Motor de template | template injection (CWE-1336) | 4, 5 | template sem avaliação de entrada |
| Parser XML | XXE (CWE-611) | 4, 5, 7 | entidades externas desligadas |
| Log4j (JNDI lookup) | Log4Shell, CVE-2021-44228 | 7, 8, 10 | log trata entrada como dado, nunca como expressão |
| OGNL no Struts | CVE-2017-5638 (Equifax) | 10 | cabeçalho tratado como dado |
| SQL no MOVEit | CVE-2023-34362 | 10 | parametrização — a mesma de sempre |
| Modelo de linguagem | injeção de prompt | 2 | separar instrução do sistema de dado do usuário |

Frase-ponte: *"A injeção que o ponto 4 descreve na consulta SQL é a mesma classe que ⟨este ponto⟩ encontra em ⟨interpretador⟩: dado interpretado como instrução. A defesa é sempre separar os dois por construção, nunca filtrar caracteres."*

---

## 2. O mapa — onde cada vulnerabilidade entra em cada ponto

### Ponto 1 — Ciclo de vida seguro e DevSecOps

| Vulnerabilidade | Onde entra | Frase-ponte |
|---|---|---|
| Injeção de SQL | o custo por fase (Boehm, 1981): a mesma concatenação custa minutos se o SAST a aponta no *pull request*, dias se o DAST a acha no noturno, meses se vira incidente | "Uma injeção de SQL ilustra a curva de Boehm: no design, é um fluxo que cruza fronteira e pede parametrização como requisito; no código, é um achado de análise estática; em produção, é vazamento de banco." |
| XSS | o gate do pipeline: DAST noturno encontra XSS refletido; CSP ausente é achado de configuração | "O shift-left pega o XSS armazenado na revisão de código e no template com escape automático; o shift-right pega o refletido na página de erro que ninguém testou." |
| CSRF | padrão seguro por default (SD3+C, Howard e Lipner): o framework que gera token anti-CSRF por padrão é o ciclo decidindo por você | "Seguro por padrão é o framework que inclui o token anti-CSRF sem que o desenvolvedor peça." |
| IDOR | requisito negativo da fase de requisitos: "não lê conta de outro" nasce da modelagem e vira teste | "A IDOR é o requisito negativo que a fase de requisitos escreve e a de testes prova." |

### Ponto 2 — Modelagem de ameaças

| Vulnerabilidade | Onde entra (STRIDE por elemento) | Frase-ponte |
|---|---|---|
| Injeção de SQL | **T** (tampering) no fluxo API → banco; o atacante altera a consulta | "No DFD, a injeção é tampering no fluxo entre o processo API e o armazenamento banco; a mitigação por categoria para T é integridade — aqui, parametrização." |
| XSS | **T** no fluxo servidor → navegador e **I** (disclosure) do que o script lê; XSS armazenado é T no armazenamento que depois alcança outros usuários | "XSS é tampering no fluxo de resposta: o atacante insere instrução onde deveria haver dado, e o navegador da vítima a executa." |
| CSRF | **S** (spoofing) da intenção do usuário no fluxo navegador → servidor: a requisição parece dele e não é | "CSRF é spoofing: o servidor recebe uma requisição autenticada que o usuário não fez." |
| IDOR | **E** (elevação) no processo API: o cliente faz o que não pode | "A IDOR é elevação de privilégio no processo que atende a rota; o controle é a verificação de dono — mediação completa." |
| SSRF | **S** e **I** via processo que busca URL: o servidor age em nome do atacante contra a rede interna | "SSRF é o processo API virando entidade confiável do lado errado da fronteira." |
| Árvore de ataque | objetivo "transferir da conta de outro" = IDOR **ou** CSRF **ou** XSS roubando sessão | "A árvore de ataque de Schneier (1999) para 'transferir de outra conta' tem três ramos OU: IDOR na API, CSRF no formulário, XSS roubando a sessão." |

### Ponto 3 — Arquitetura e projeto seguros

| Vulnerabilidade | Princípio violado | Frase-ponte |
|---|---|---|
| IDOR | **mediação completa** (Saltzer e Schroeder) e "autorize depois de autenticar" (IEEE CSD #3) | "A IDOR é a violação mais comum de mediação completa: o acesso foi autenticado e não foi autorizado por objeto." |
| XSS, SQLi | **separar dado de instrução** (IEEE CSD #4); **defesa em profundidade**: codificação de saída *e* CSP *e* `HttpOnly` | "A defesa em profundidade contra XSS tem três camadas independentes: escape por contexto, CSP e cookie HttpOnly; cada uma segura o que a anterior deixou passar." |
| CSRF | **padrões à prova de falha**: `SameSite=Lax` como padrão do navegador (2020) é o princípio virando plataforma | "Quando o Chrome tornou SameSite=Lax padrão, aplicou o princípio de padrões à prova de falha a toda a web." |
| SSRF | **menor privilégio** e **deputado confuso** (Hardy, 1988): o servidor usa a própria autoridade em nome de quem não a tem — Capital One, 2019 | "SSRF é o deputado confuso: o servidor acessa o serviço de metadados com a autoridade dele, a pedido do atacante." |
| Injeção | **economia de mecanismo**: um ponto único de acesso a dados (repositório parametrizado) em vez de consultas espalhadas | "Concentrar o acesso ao banco em uma camada parametrizada é economia de mecanismo aplicada à injeção." |

### Ponto 5 — Codificação segura e erros de memória

| Vulnerabilidade | Onde entra | Frase-ponte |
|---|---|---|
| Injeção de SQL | a mesma classe que string de formato e injeção de comando: dado como instrução; regra CERT equivalente (IDS, STR) | "O que `printf(msg)` é para C, `\"... WHERE id = \" + id` é para SQL: a entrada vira instrução. A regra é a mesma: o dado entra como argumento, nunca como texto do comando." |
| XSS | codificação de saída por contexto é o caso web da regra "falhar fechado e separar dado de instrução"; sanitizar ≠ escapar | "Escapar transforma tudo em texto; sanitizar mantém HTML permitido. Confundir os dois é o erro de código por trás da maioria dos XSS armazenados." |
| Validação de entrada | "analise, não valide": o `id` da conta vira tipo `IdDeConta` que só existe válido — e a IDOR vira impossível de esquecer na quinta rota | "O tipo que só existe válido é o controle de código que impede a IDOR por esquecimento." |
| Heartbleed × validação | o campo de tamanho do pacote é dado do pacote, como o `Content-Length` é dado da requisição | "Confiar no comprimento declarado é o Heartbleed; confiar no `id` declarado é a IDOR. É a mesma regra: toda entrada é hostil, inclusive o que parece metadado." |

### Ponto 6 — Autenticação, autorização e sessão

| Vulnerabilidade | Onde entra | Frase-ponte |
|---|---|---|
| CSRF | a identidade é um token que viaja em cookie enviado automaticamente; o escopo de envio (`SameSite`) e o token sincronizador são controles de sessão | "CSRF existe porque o cookie de sessão vai em toda requisição ao site; é um problema de escopo da sessão, não da aplicação." |
| XSS | rouba a sessão (`document.cookie` sem `HttpOnly`; JWT em `localStorage`) e usa a sessão enquanto a página está aberta | "Token em localStorage é XSS levando a sessão; cookie HttpOnly é XSS usando a sessão só enquanto a aba está aberta — ainda assim, melhor." |
| IDOR / BOLA | é a autorização por objeto ausente; RBAC não a resolve porque papel não sabe de quem é a conta | "A IDOR mostra o limite do RBAC: 'cliente' é papel certo para ler conta, mas não diz qual conta; precisa de ABAC ou verificação de dono." |
| Fixação de sessão | prima do CSRF: o atacante planta o identificador; a vítima loga; rotação no login mata | "Rotacionar o identificador no login é para a fixação o que o token é para o CSRF." |

### Ponto 7 — Testes de segurança

| Vulnerabilidade | Qual janela a encontra | Frase-ponte |
|---|---|---|
| Injeção de SQL | **SAST** por análise de *taint* (fonte `params` → destino consulta, sem parametrização); **DAST** por erro de banco ou tempo | "A injeção de SQL é o caso clássico de taint: fonte, destino, ausência de neutralizador — e também o caso em que o SAST erra quando o ORM parametriza por dentro." |
| XSS | **DAST** (refletido); **IAST** (linha + caminho); armazenado escapa do DAST e exige teste escrito | "DAST acha o XSS refletido na página de erro; o armazenado, que aparece em outra página, exige caso de teste escrito." |
| IDOR | **matriz de autorização** gerada e **teste de intrusão** com duas identidades; nenhum scanner a acha sozinho | "A IDOR é a vulnerabilidade que separa teste automatizado de teste escrito: só uma matriz com dois sujeitos a encontra." |
| SSRF | teste escrito com URL para `169.254.169.254`; **fuzzing de API** encontra 500s no endpoint de webhook | "O caso de abuso para SSRF é literal: a URL do serviço de metadados deve ser rejeitada antes da conexão." |
| XXE | **SCA** aponta o parser com CVE; **fuzzing** com sanitizador derruba o parser | "A biblioteca XML transitiva com CVE é achado de SCA; o parser que aceita entidade externa é achado de teste escrito." |

### Ponto 8 — Cadeia de suprimentos

| Vulnerabilidade | Onde entra | Frase-ponte |
|---|---|---|
| XSS | **script de terceiro comprometido é XSS com convite**: polyfill.io (2024, domínio comprado e JS malicioso servido a mais de 100 mil sítios); Magecart/British Airways (2018, roubo de cartão por script injetado) | "Quando o CDN que serve seu script é comprometido, o atacante tem XSS em todo sítio que o carrega — SRI e CSP são os controles de cadeia de suprimentos do navegador." |
| Injeção | dependência com injeção conhecida (Log4Shell é injeção em biblioteca de log) é a cadeia alheia entregando a vulnerabilidade do ponto 4 | "Log4Shell é uma injeção — a string de log avaliada como expressão JNDI — que chegou por dependência transitiva: ponto 4 entregue pelo ponto 8." |
| IDOR / BOLA | o pacote interno confundido (*dependency confusion*) é IDOR do registro: pedir um nome e receber o de outro | "Dependency confusion é a IDOR dos registros de pacotes: o gerenciador pede `empresa-utils` e recebe o do atacante porque ninguém verificou o dono." |

### Ponto 9 — APIs, microsserviços e contêineres

| Vulnerabilidade | Onde entra | Frase-ponte |
|---|---|---|
| IDOR | **API1 BOLA** do OWASP API Top 10 (2023); o gateway não a vê porque não sabe de quem é a conta | "BOLA é a IDOR com nome de API, e é a primeira da lista de 2023 porque o gateway valida token e esquema, mas não dono." |
| SSRF | **API7**; webhook e importação por URL; metadados da instância; egress restrito | "A SSRF em API é o webhook que busca a URL do cliente; o controle é lista de destinos pós-DNS e rede de saída fechada." |
| CSRF | não se aplica a API com token em `Authorization` (o navegador não anexa o cabeçalho sozinho) — e se aplica à API que usa cookie | "A API que autentica por cabeçalho Authorization não sofre CSRF; a que usa cookie, sim — por isso a escolha de onde vai o token é decisão de arquitetura." |
| XSS | SPA consome JSON da API e escreve no DOM: XSS de DOM; *mass assignment* (API3) é a entrada não filtrada da API | "Na API, o XSS mora no cliente que escreve a resposta no DOM; na entrada, mass assignment é aceitar propriedades que o contrato não declarou." |
| Injeção | GraphQL e gRPC tipam, mas não impedem injeção no resolvedor que concatena; esquema é validação de forma, não de uso | "O esquema OpenAPI valida a forma do dado; a parametrização no serviço decide se ele vira instrução." |

### Ponto 10 — Gestão de vulnerabilidades

| Vulnerabilidade | Onde entra | Frase-ponte |
|---|---|---|
| Injeção | **os três casos do ponto são injeção**: Equifax (OGNL no Struts, CVE-2017-5638), Log4Shell (JNDI, CVE-2021-44228), MOVEit (SQL, CVE-2023-34362) | "As três crises de gestão de vulnerabilidades mais citadas são injeções: Struts, Log4j, MOVEit. A classe do ponto 4 é a que mais vira CVE crítica em dependência." |
| XSS vs SQLi | ler o vetor CVSS: XSS refletido típico `AV:N/AC:L/PR:N/UI:R/C:L/I:L` ≈ 6,1; SQLi sem autenticação `AV:N/AC:L/PR:N/UI:N/C:H/I:H/A:H` = 9,8 | "O vetor explica a nota: XSS refletido exige interação do usuário e vaza pouco; SQLi sem autenticação toma o banco. Mas o KEV e o EPSS podem inverter a prioridade." |
| WAF como mitigação | regra contra o padrão da injeção ou do XSS enquanto a correção não sai — *virtual patching* | "A regra de WAF contra a injeção é mitigação temporária: reduz a classe de risco até a parametrização chegar; não fecha o item." |
| CWE-79 e CWE-89 no CWE Top 25 | XSS em 1º e SQLi em 3º (2024): são o vocabulário formal do que a gestão prioriza | "Quando o relatório de gestão diz CWE-79, está dizendo XSS; o CWE é o vocabulário que liga a vulnerabilidade concreta à classe." |

---

## 3. Como usar sem desviar do ponto

1. **Uma ponte por bloco, no máximo duas.** A vulnerabilidade web entra no exemplo guiado, no caso real ou na ilustração de um princípio. Se o texto do ponto 8 fala de XSS por três parágrafos, virou texto do ponto 4.
2. **Sempre com a frase-ponte.** A frase diz ao leitor por que aquilo está ali: "a mesma classe", "o caso web de", "o controle de cadeia de suprimentos do navegador".
3. **Nomear a classe, não só o ataque.** "Dado interpretado como instrução" é a ideia; XSS e SQLi são as instâncias. Nomear a ideia é domínio; listar instâncias é memória.
4. **Fechar voltando ao ponto.** Depois do exemplo, a frase seguinte é do ponto sorteado: "Esse é o papel do ⟨controle do ponto⟩ no ciclo."
