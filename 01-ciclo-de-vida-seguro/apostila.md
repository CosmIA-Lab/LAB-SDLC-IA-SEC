# Ponto 1A — Ciclo de vida de desenvolvimento seguro (SDLC seguro)

## Objetivo

Entender o SDLC seguro: o que é, as seis fases, as técnicas de cada fase e por que segurança cedo custa menos.

---

## 1. O que é o SDLC

SDLC = **Software Development Life Cycle** — ciclo de vida de desenvolvimento de software.

É o caminho completo que um software percorre, da ideia até a aposentadoria. Não é ferramenta, é processo.

Fases clássicas:

1. Requisitos
2. Design
3. Implementação
4. Verificação
5. Operação
6. Descontinuação

O SDLC responde uma pergunta: **o que fazer em cada fase**.

---

## 2. As seis fases do SDLC seguro

O SDLC seguro é o mesmo ciclo, com segurança embutida em cada fase — não um teste no final.

### Fase 1 — Requisitos

**Técnica:** identificação de ativos + análise de ameaças + especificação de requisitos de segurança.

- Ativos: dados de clientes, credenciais, dinheiro, chaves de API, código-fonte.
- Ameaças: acesso indevido, alteração, destruição, indisponibilidade.
- Saída: lista de requisitos — "o que o sistema deve garantir", não "como fazer".

Exemplo de requisito: "todo acesso exige autenticação", "dados sensíveis criptografados em trânsito".

### Fase 2 — Design

**Técnica:** arquitetura segura com princípios de design.

- **Least privilege:** cada componente recebe só o mínimo de acesso necessário.
- **Defense in depth:** várias camadas de proteção; uma falha não derruba tudo.
- **Fail secure:** em caso de erro, o sistema nega acesso (não abre).
- **Zero trust:** nunca confiar automaticamente, verificar sempre.

É a fase mais barata para corrigir erro: mudar um desenho custa menos que reescrever código.

### Fase 3 — Implementação

**Técnica:** codificação segura.

- Evitar padrões conhecidos de falha: buffer overflow, SQL injection, uso de funções inseguras.
- Revisão de código com olhar de segurança.
- SAST rodando em cada commit.

### Fase 4 — Verificação

**Técnica:** testes de segurança.

- **SAST:** analisa código-fonte sem executá-lo.
- **DAST:** testa a aplicação rodando, como um atacante.
- **SCA:** verifica dependências de terceiros.
- **Pentest:** simula ataque real.

### Fase 5 — Operação

**Técnica:** monitoramento e resposta.

- Rastrear CVEs e saber quais afetam o que roda.
- Resposta a incidentes.
- Patch management sem derrubar o serviço.

### Fase 6 — Descontinuação

**Técnica:** descomissionamento seguro.

- Remoção segura de dados.
- Revogação de credenciais.
- Transferência controlada.

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

## 4. Técnicas para memorizar

| Fase | Técnica central |
|---|---|
| Requisitos | Identificar ativos e ameaças |
| Design | Least privilege + defense in depth |
| Implementação | Codificação segura |
| Verificação | SAST, DAST, SCA, pentest |
| Operação | Monitorar CVEs + resposta a incidentes |
| Descontinuação | Remoção segura de dados |

---

## Referências

- Microsoft SDL — learn.microsoft.com (procure "Security Development Lifecycle")
- NIST SSDF — nist.gov/publications/sp-800-218
- OWASP SAMM — owaspsamm.org
