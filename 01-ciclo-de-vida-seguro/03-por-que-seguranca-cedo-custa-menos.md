# 3. Por que segurança cedo custa menos

A lógica central do SDLC seguro: quanto mais cedo a segurança entra,
mais barato é corrigir.

## A regra

Uma falha encontrada no commit custa horas. A mesma falha em
produção custa dias ou semanas.

## Por quê

- No commit, o código é pequeno e o contexto está fresco.
- Em produção, a falha já afetou dados, clientes ou receita.
- Corrigir em produção exige investigação, rollback e comunicação.

## Exemplo prático

Uma SQL injection corrigida no pull request: o desenvolvedor reescreve
a query e adiciona um teste. Uma SQL injection em produção: incidente,
investigação forense, notificação de clientes, patch emergencial.

## Próximo capítulo

[O que é DevSecOps →](04-o-que-e-devsecops.md)
