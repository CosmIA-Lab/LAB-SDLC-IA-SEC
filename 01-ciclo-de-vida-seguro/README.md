# Ponto 1 — Ciclo de vida de desenvolvimento seguro e DevSecOps

## Objetivo

Entender o SDLC seguro: o que é, as seis fases, as técnicas de cada fase, e como o DevSecOps executa esse ciclo de forma contínua e automatizada.

---

## 1. Overview visual — o ciclo completo

```mermaid
flowchart LR
    A["1. Requisitos<br/>Ativos e ameaças"] --> B["2. Design<br/>Least privilege<br/>Defense in depth"]
    B --> C["3. Implementação<br/>Codificação segura<br/>SAST no commit"]
    C --> D["4. Verificação<br/>SAST, DAST<br/>SCA, pentest"]
    D --> E["5. Operação<br/>CVEs<br/>Resposta a incidentes"]
    E --> F["6. Descontinuação<br/>Remoção segura<br/>de dados"]
    F -.->|"novo ciclo"| A
```

A lógica central: quanto mais cedo a segurança entra, mais barato é corrigir. Uma falha no commit custa horas; a mesma em produção custa dias ou semanas.

---

## 2. As seis fases do SDLC seguro

O SDLC seguro é o mesmo ciclo de vida de software, com segurança embutida em cada fase — não um teste no final.

### Fase 1 — Requisitos

**Técnica central:** identificar ativos, classificar dados e escrever requisitos de segurança verificáveis.

A fase responde "o que o sistema deve garantir", não "como implementar". Se o requisito não dá para testar, ele não serve.

```mermaid
flowchart LR
    A[Ativos] --> B[Classificação]
    B --> C[CIA]
    C --> D[Requisitos]
    D --> E[Abuse cases]
    E --> F[Critérios de aceite]
    F --> G[Rastreio]
```

```mermaid
flowchart TB
    F1["Fase 1 — Requisitos<br/>o que o sistema deve garantir"]
    F1 --> A["Ativos<br/>dados, segredos, funções, infra"]
    F1 --> CL["Classificação<br/>Público, Interno, Confidencial, Restrito"]
    F1 --> CIA["CIA<br/>Confidencialidade, Integridade, Disponibilidade"]
    F1 --> R["Requisitos<br/>funcionais, não funcionais, restrições"]
    F1 --> AC["Abuse cases<br/>o que o ator NÃO deve conseguir fazer"]
    F1 --> CA["Critérios de aceite<br/>teste que passa ou falha"]
    F1 --> RT["Rastreio<br/>ativo → requisito → controle → teste"]
    R --> CA
    AC --> CA
    CA --> RT
```

#### Princípios

- **Verificabilidade** — se o requisito não dá para testar, ele não serve.
- **Rastreabilidade** — cada requisito liga a um controle e a um teste: ativo → requisito → controle → teste.
- **Negação explícita** — abuse case: o que um ator não deve conseguir fazer vira requisito negativo.

#### Técnicas

- **Identificação de ativos** — dados, segredos, funções, infra.
- **Classificação de dados** — Público, Interno, Confidencial, Restrito.
- **CIA** — Confidencialidade, Integridade, Disponibilidade: a lente do requisito.
- **Tipos de requisito** — funcional, não funcional, restrição.
- **Critério de aceite** — cada requisito precisa de um teste que passa ou falha.
- **Premissas e fora de escopo** — escrever o que o sistema assume e o que não promete.

#### Tópicos para memorizar

- **Técnica:** ativos, classificação, CIA, requisitos verificáveis
- **Princípio:** verificável, rastreável, negação explícita
- **Regra de ouro:** requisito ruim = "seja seguro"; requisito bom = "rota autenticada rejeita token expirado com 401"

### Fase 2 — Design

**Técnica central:** arquitetura segura com princípios de design.

É a fase mais barata para corrigir erro: mudar um desenho custa menos que reescrever código.

```mermaid
flowchart TB
    D["Fase 2 — Design<br/>Arquitetura segura com princípios de design"]
    D --> LP["Least privilege<br/>mínimo de acesso por componente"]
    D --> DID["Defense in depth<br/>várias camadas; uma falha não derruba tudo"]
    D --> FS["Fail secure<br/>erro nega acesso, nunca libera"]
    D --> ZT["Zero trust<br/>verificar sempre, nunca confiar por padrão"]
    LP --> R["Regra de ouro<br/>design errado custa uma reunião<br/>código errado custa meses"]
    DID --> R
    FS --> R
    ZT --> R
```

#### Princípios

- **Least privilege** — cada componente recebe só o mínimo de acesso necessário.
  - Exemplo: microsserviço de pagamentos só lê o banco de pagamentos; não lê o de usuários.
- **Defense in depth** — várias camadas de proteção, para que uma falha não derrube tudo.
  - Exemplo: WAF + SAST + pentest; se uma camada falha, as outras seguram.
- **Fail secure** — em caso de erro, o sistema nega acesso.
  - Exemplo: se o serviço de autenticação cair, o login é bloqueado — não liberado.
  - O oposto é o fail open: erro libera tudo por conveniência.
- **Zero trust** — nunca confiar automaticamente, verificar sempre.
  - Exemplo: mesmo dentro da rede interna, cada requisição é autenticada e autorizada.
  - Acabou a ideia de "quem tá dentro da rede é confiável".

#### Técnicas

- **Diagrama de fluxo de dados** — base para aplicar os princípios a cada elemento do sistema.
- **Análise de superfície de ataque** — listar pontos de entrada e priorizar por risco.

#### Tópicos para memorizar

- **Técnica:** arquitetura segura com princípios de design
- **Princípio 1 — Least privilege:** mínimo de acesso por componente
- **Princípio 2 — Defense in depth:** várias camadas; uma falha não derruba tudo
- **Princípio 3 — Fail secure:** erro nega acesso (nunca libera)
- **Princípio 4 — Zero trust:** verificar sempre, nunca confiar por padrão
- **Regra de ouro:** design errado custa uma reunião; código errado custa meses

### Fase 3 — Implementação

**Técnica:** codificação segura.

Evitar padrões conhecidos de falha: buffer overflow, SQL injection, uso de funções inseguras. Revisão de código com olhar de segurança. SAST rodando em cada commit.

### Fase 4 — Verificação

**Técnica:** testes de segurança.

SAST analisa o código-fonte sem executá-lo. DAST testa a aplicação rodando, como um atacante. SCA verifica dependências de terceiros. Pentest simula ataque real.

### Fase 5 — Operação

**Técnica:** monitoramento e resposta.

Rastrear CVEs e saber quais afetam o que roda. Resposta a incidentes. Patch management sem derrubar o serviço.

### Fase 6 — Descontinuação

**Técnica:** descomissionamento seguro.

Remoção segura de dados. Revogação de credenciais. Transferência controlada.

---

## 3. Por que segurança cedo custa menos

### A regra

Uma falha no commit custa horas. A mesma falha em produção custa dias ou semanas.

### Por quê

- No commit, o código é pequeno e o contexto está fresco.
- Em produção, a falha já afetou dados, clientes ou receita.
- Corrigir em produção exige investigação, rollback e comunicação com impactados.

### Exemplo

SQL injection corrigida no pull request: reescreve a query, adiciona teste. Fim.

A mesma em produção: incidente, investigação forense, notificação de clientes, patch emergencial fora do horário.

---

## 4. O que é DevSecOps

DevSecOps = **Development + Security + Operations**.

É a prática de integrar segurança no pipeline de CI/CD, de forma automatizada e contínua. Segurança deixa de ser etapa separada no final e roda junto com tudo.

O nome junta três áreas:

- **Development** — o time que escreve o código
- **Security** — a camada de segurança: ferramentas e práticas
- **Operations** — o time que cuida de deploy, monitoramento e infraestrutura

### A ideia central

Segurança passa a ser responsabilidade compartilhada pelos três, não de um time isolado. Cada commit dispara verificações automáticas; se algo falhar, o pipeline bloqueia o deploy.

### O oposto

Modelo tradicional: o time de segurança recebia o sistema pronto, fazia um pentest no final, encontrava falhas caras e devolvia. Todo mundo perdia tempo.

---

## 5. Shift-left e shift-right

Duas práticas centrais do DevSecOps.

### Shift-left

Trazer segurança para o mais cedo possível. Em vez de testar no final, testar em cada commit: SAST no push, SCA nas dependências, secrets scanning antes do merge.

### Shift-right

Monitorar em produção. CVEs novos aparecem todo dia; o time precisa saber quais afetam o que roda e responder rápido.

### Juntas

Shift-left previne. Shift-right detecta. Um sem o outro deixa buracos: só shift-left ignora o que já está em produção; só shift-right corrige tarde demais.

---

## 6. Pipeline de CI/CD com segurança

O pipeline é a sequência automatizada de etapas do commit ao deploy. Com segurança embutida, cada etapa ganha uma verificação.

### Etapas típicas

1. **Commit** — o desenvolvedor envia o código
2. **Build** — compilação ou empacotamento
3. **Testes** — testes automatizados
4. **Análise de segurança** — SAST, SCA, secrets scanning
5. **Deploy em staging** — ambiente de teste
6. **DAST** — a aplicação rodando é testada como um atacante
7. **Deploy em produção** — só se tudo passou

### Regra de ouro

Se uma verificação falhar, o pipeline para. Nada vai para produção com falha de segurança conhecida.

---

## 7. Ferramentas do DevSecOps

Cada letra do DevSecOps tem ferramentas correspondentes.

### SAST — Static Application Security Testing

Analisa o código-fonte sem executá-lo. Encontra padrões de falha conhecidos: SQL injection, buffer overflow, funções inseguras.

Exemplos: Semgrep, SonarQube, CodeQL.

### DAST — Dynamic Application Security Testing

Testa a aplicação rodando, como um atacante. Envia requisições maliciosas e observa as respostas.

Exemplos: OWASP ZAP, Burp Suite.

### SCA — Software Composition Analysis

Verifica dependências de terceiros. Bibliotecas com CVEs conhecidas são sinalizadas.

Exemplos: Dependabot, Snyk, Trivy.

### Fuzzing

Envia entradas aleatórias ou malformadas e observa se o programa quebra. Bom para falhas de memória e parsing.

### Secrets scanning

Procura credenciais vazadas no código: senhas, tokens, chaves de API.

Exemplos: GitGuardian, gitleaks, trufflehog.

---

## 8. SDLC seguro vs DevSecOps

### SDLC seguro

É o **processo**: as seis fases com segurança embutida. Responde "o que fazer em cada fase".

### DevSecOps

É a **prática**: como esse processo roda no dia a dia, com segurança automatizada no pipeline. Responde "como garantir que a segurança aconteça em cada fase, de forma contínua".

### Resumo

| | SDLC seguro | DevSecOps |
|---|---|---|
| Natureza | Processo | Prática |
| Pergunta | O que fazer? | Como fazer? |
| Escala | Qualquer projeto | Projetos com CI/CD |

Um não substitui o outro: o DevSecOps é a forma de executar o SDLC seguro em escala.

---

## 9. Técnicas para memorizar

| Fase / Conceito | Técnica |
|---|---|
| Requisitos | Ativos, classificação, CIA, abuse case, critério verificável |
| Design | Arquitetura segura: least privilege, defense in depth, fail secure, zero trust |
| Implementação | Codificação segura |
| Verificação | SAST, DAST, SCA, pentest |
| Operação | Monitorar CVEs + resposta a incidentes |
| Descontinuação | Remoção segura de dados |
| DevSecOps | Dev + Sec + Ops integrados no pipeline |
| Shift-left | Segurança no início do ciclo |
| Shift-right | Monitoramento em produção |

---

## Referências

- Microsoft SDL — learn.microsoft.com (procure "Security Development Lifecycle")
- NIST SSDF — nist.gov/publications/sp-800-218
- OWASP SAMM — owaspsamm.org
- OWASP ASVS — catálogo de requisitos verificáveis
