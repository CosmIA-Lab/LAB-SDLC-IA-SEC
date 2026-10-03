# 4. O que é DevSecOps

DevSecOps é a prática de integrar segurança no pipeline de CI/CD,
de forma automatizada e contínua, em vez de tratar segurança como
etapa separada no final do desenvolvimento.

O nome junta três áreas:

- **Development** — o time que escreve o código
- **Security** — a camada de segurança (ferramentas e práticas)
- **Operations** — o time que cuida de deploy, monitoramento e infraestrutura

## A ideia central

Segurança deixa de ser responsabilidade de um time isolado e passa a
ser compartilhada pelos três. Cada commit dispara verificações
automáticas; se algo falhar, o pipeline bloqueia o deploy.

## O oposto

O modelo tradicional: o time de segurança recebia o sistema pronto,
fazia um pentest no final, encontrava falhas caras de corrigir e
devolvia.

## Próximo capítulo

[Shift-left e shift-right →](05-shift-left-e-shift-right.md)
