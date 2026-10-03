# Ponto 1 — Ciclo de vida de desenvolvimento seguro e DevSecOps

## Objetivo

Entender o que é o SDLC seguro, suas seis fases, e como o DevSecOps
executa esse ciclo de forma contínua e automatizada.

---

## 1. O que é o SDLC

SDLC é a sigla em inglês para **Software Development Life Cycle** —
ciclo de vida de desenvolvimento de software.

Pensa assim: é o caminho completo que um software percorre, da ideia
até a aposentadoria. Não é uma ferramenta, é o processo.

As fases clássicas são:

1. Requisitos
2. Design
3. Implementação
4. Verificação
5. Operação
6. Descontinuação

Ou seja, o SDLC responde uma pergunta só: **o que fazer em cada fase**.

Vale para qualquer coisa — desde um site simples até um monólito com
milhões de registros em produção.

---

## 2. As seis fases do SDLC seguro

O SDLC seguro é o mesmo ciclo de vida, só que com segurança embutida
em cada fase. Em vez de deixar a segurança para um teste no final,
ela entra desde o começo.

### Fase 1 — Requisitos

Aqui você identifica os ativos — dados de clientes, credenciais,
dinheiro, chaves de API — e as ameaças que pesam sobre eles. A saída
é uma lista de requisitos de segurança: o que o sistema precisa
garantir, não como fazer.

### Fase 2 — Design

É hora de aplicar least privilege (cada componente recebe só o mínimo
de acesso que precisa) e defense in depth (várias camadas de proteção,
para que uma falha não derrube tudo). É a fase mais barata para
corrigir erro — mudar um desenho custa muito menos do que reescrever
código depois.

### Fase 3 — Implementação

Codificação segura: evitar padrões conhecidos de falha, como buffer
overflow e SQL injection. Revisão de código com olhar de segurança,
e SAST rodando em cada commit.

### Fase 4 — Verificação

SAST analisa o código-fonte sem executá-lo. DAST testa a aplicação
rodando, como um atacante faria. SCA verifica dependências de
terceiros. Testes de penetração simulam um ataque real.

### Fase 5 — Operação

Monitorar vulnerabilidades novas — os famosos CVEs — e saber quais
afetam o que roda. Resposta a incidentes e patch management sem
derrubar o serviço.

### Fase 6 — Descontinuação

Remoção segura de dados, revogação de credenciais e transferência
controlada. É a fase que todo mundo esquece — e que costuma doer
quando esquece.

---

## 3. Por que segurança cedo custa menos

A lógica central do SDLC seguro é simples: quanto mais cedo a
segurança entra, mais barato é corrigir.

### A regra

Uma falha encontrada no commit custa horas. A mesma falha em
produção custa dias ou semanas.

### Por quê

- No commit, o código é pequeno e o contexto ainda está fresco.
- Em produção, a falha já afetou dados, clientes ou receita.
- Corrigir em produção exige investigação, rollback e comunicação
  com quem foi impactado.

### Um exemplo

Uma SQL injection corrigida no pull request: o desenvolvedor reescreve
a query e adiciona um teste. Fim.

A mesma SQL injection em produção: incidente, investigação forense,
notificação de clientes, patch emergencial fora do horário. Não é a
mesma conta.

---

## 4. O que é DevSecOps

DevSecOps é a prática de integrar segurança no pipeline de CI/CD,
de forma automatizada e contínua. Em vez de tratar segurança como
uma etapa separada no final do desenvolvimento, ela roda junto com
tudo.

O nome junta três áreas:

- **Development** — o time que escreve o código
- **Security** — a camada de segurança: ferramentas e práticas
- **Operations** — o time que cuida de deploy, monitoramento e
  infraestrutura

### A ideia central

Segurança deixa de ser responsabilidade de um time isolado e passa a
ser compartilhada pelos três. Cada commit dispara verificações
automáticas; se algo falhar, o pipeline bloqueia o deploy.

### O oposto disso

O modelo tradicional: o time de segurança recebia o sistema pronto,
fazia um pentest no final, encontrava falhas caras de corrigir e
devolvia. Todo mundo perdia tempo.

---

## 5. Shift-left e shift-right

Duas práticas centrais do DevSecOps.

### Shift-left

Trazer segurança para o mais cedo possível no ciclo. Em vez de
testar no final, testar em cada commit: SAST no push, SCA nas
dependências, secrets scanning antes do merge.

### Shift-right

Monitorar em produção. Vulnerabilidades novas aparecem todo dia
(CVEs); o time precisa saber quais afetam o que roda e responder
rápido.

### Juntas

Shift-left previne. Shift-right detecta. Um sem o outro deixa
buracos: só shift-left ignora o que já está em produção; só
shift-right corrige tarde demais.

---

## 6. Pipeline de CI/CD com segurança

O pipeline é a sequência automatizada de etapas que o código percorre
do commit ao deploy. Com segurança embutida, cada etapa ganha uma
verificação.

### Etapas típicas

1. **Commit** — o desenvolvedor envia o código
2. **Build** — o código é compilado ou empacotado
3. **Testes** — testes automatizados rodam
4. **Análise de segurança** — SAST, SCA, secrets scanning
5. **Deploy em staging** — ambiente de teste
6. **DAST** — a aplicação rodando é testada como um atacante faria
7. **Deploy em produção** — só se tudo passou

### Regra de ouro

Se uma verificação falhar, o pipeline para. Nada vai para produção
com falha de segurança conhecida.

---

## 7. Ferramentas do DevSecOps

Cada letra do DevSecOps tem ferramentas correspondentes.

### SAST — Static Application Security Testing

Analisa o código-fonte sem executá-lo. Encontra padrões de falha
conhecidos: SQL injection, buffer overflow, uso de funções inseguras.
Exemplos: Semgrep, SonarQube, CodeQL.

### DAST — Dynamic Application Security Testing

Testa a aplicação rodando, como um atacante faria. Envia requisições
maliciosas e observa as respostas. Exemplos: OWASP ZAP, Burp Suite.

### SCA — Software Composition Analysis

Verifica as dependências de terceiros. Bibliotecas com vulnerabilidades
conhecidas (CVEs) são sinalizadas. Exemplos: Dependabot, Snyk, Trivy.

### Fuzzing

Envia entradas aleatórias ou malformadas para o programa e observa
se ele quebra. Bom para encontrar falhas de memória e parsing.

### Secrets scanning

Procura credenciais vazadas no código: senhas, tokens, chaves de API.
Exemplos: GitGuardian, gitleaks, trufflehog.

---

## 8. SDLC seguro vs DevSecOps

### SDLC seguro

É o **processo**: as seis fases com segurança embutida. Responde
"o que fazer em cada fase".

### DevSecOps

É a **prática**: como esse processo roda no dia a dia, com segurança
automatizada no pipeline. Responde "como garantir que a segurança
aconteça em cada fase, de forma contínua".

### Resumo

| | SDLC seguro | DevSecOps |
|---|---|---|
| Natureza | Processo | Prática |
| Pergunta | O que fazer? | Como fazer? |
| Escala | Qualquer projeto | Projetos com CI/CD |

Um não substitui o outro: o DevSecOps é a forma de executar o SDLC
seguro em escala.

---

## Referências

- Microsoft SDL — learn.microsoft.com (procure "Security Development Lifecycle")
- NIST SSDF — nist.gov/publications/sp-800-218
- OWASP SAMM — owaspsamm.org
