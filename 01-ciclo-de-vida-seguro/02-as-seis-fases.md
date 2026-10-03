# 2. As seis fases do SDLC seguro

O SDLC seguro é o mesmo ciclo de vida, com segurança embutida em
cada fase — em vez de ser um teste no final.

## Fase 1 — Requisitos

Identificar os ativos (dados de clientes, credenciais, dinheiro,
chaves de API) e as ameaças que pesam sobre eles. A saída é uma
lista de requisitos de segurança: o que o sistema deve garantir,
não como fazer.

## Fase 2 — Design

Aplicar least privilege (cada componente recebe só o mínimo de
acesso) e defense in depth (várias camadas de proteção). É a fase
mais barata para corrigir erro.

## Fase 3 — Implementação

Codificação segura: evitar padrões conhecidos de falha como buffer
overflow e SQL injection. Revisão de código com olhar de segurança
e SAST rodando em cada commit.

## Fase 4 — Verificação

SAST analisa o código-fonte sem executá-lo. DAST testa a aplicação
rodando, como um atacante faria. SCA verifica dependências de
terceiros. Testes de penetração simulam ataque real.

## Fase 5 — Operação

Monitorar vulnerabilidades novas (CVEs) e saber quais afetam o que
roda. Resposta a incidentes e patch management sem derrubar o serviço.

## Fase 6 — Descontinuação

Remoção segura de dados, revogação de credenciais e transferência
controlada. É a fase que todo mundo esquece.

## Próximo capítulo

[Por que segurança cedo custa menos →](03-por-que-seguranca-cedo-custa-menos.md)
