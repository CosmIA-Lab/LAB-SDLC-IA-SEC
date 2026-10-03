# 6. Pipeline de CI/CD com segurança

O pipeline é a sequência automatizada de etapas que o código percorre
do commit ao deploy. Com segurança embutida, cada etapa ganha uma
verificação.

## Etapas típicas

1. **Commit** — o desenvolvedor envia o código
2. **Build** — o código é compilado ou empacotado
3. **Testes** — testes automatizados rodam
4. **Análise de segurança** — SAST, SCA, secrets scanning
5. **Deploy em staging** — ambiente de teste
6. **DAST** — a aplicação rodando é testada como um atacante faria
7. **Deploy em produção** — só se tudo passou

## Regra de ouro

Se uma verificação falhar, o pipeline para. Nada vai para produção
com falha de segurança conhecida.

## Próximo capítulo

[Ferramentas do DevSecOps →](07-ferramentas-do-devsecops.md)
