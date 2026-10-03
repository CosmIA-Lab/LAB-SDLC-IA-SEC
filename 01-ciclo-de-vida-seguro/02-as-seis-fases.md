# 2. As seis fases do SDLC seguro

O SDLC seguro é o mesmo ciclo de vida, só que com segurança embutida
em cada fase. Em vez de deixar a segurança para um teste no final,
ela entra desde o começo.

## Fase 1 — Requisitos

Aqui você identifica os ativos — dados de clientes, credenciais,
dinheiro, chaves de API — e as ameaças que pesam sobre eles. A saída
é uma lista de requisitos de segurança: o que o sistema precisa
garantir, não como fazer.

## Fase 2 — Design

É hora de aplicar least privilege (cada componente recebe só o mínimo
de acesso que precisa) e defense in depth (várias camadas de proteção,
para que uma falha não derrube tudo). É a fase mais barata para
corrigir erro — mudar um desenho custa muito menos do que reescrever
código depois.

## Fase 3 — Implementação

Codificação segura: evitar padrões conhecidos de falha, como buffer
overflow e SQL injection. Revisão de código com olhar de segurança,
e SAST rodando em cada commit.

## Fase 4 — Verificação

SAST analisa o código-fonte sem executá-lo. DAST testa a aplicação
rodando, como um atacante faria. SCA verifica dependências de
terceiros. Testes de penetração simulam um ataque real.

## Fase 5 — Operação

Monitorar vulnerabilidades novas — os famosos CVEs — e saber quais
afetam o que roda. Resposta a incidentes e patch management sem
derrubar o serviço.

## Fase 6 — Descontinuação

Remoção segura de dados, revogação de credenciais e transferência
controlada. É a fase que todo mundo esquece — e que costuma doer
quando esquece.

## Próximo capítulo

[Por que segurança cedo custa menos →](03-por-que-seguranca-cedo-custa-menos.md)
