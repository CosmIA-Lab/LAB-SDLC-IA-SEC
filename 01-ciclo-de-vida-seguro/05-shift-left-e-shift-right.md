# 5. Shift-left e shift-right

Duas práticas centrais do DevSecOps.

## Shift-left

Trazer segurança para o mais cedo possível no ciclo. Em vez de
testar no final, testar em cada commit: SAST no push, SCA nas
dependências, secrets scanning antes do merge.

## Shift-right

Monitorar em produção. Vulnerabilidades novas aparecem todo dia
(CVEs); o time precisa saber quais afetam o que roda e responder
rápido.

## Juntas

Shift-left previne. Shift-right detecta. Um sem o outro deixa
buracos: só shift-left ignora o que já está em produção; só
shift-right corrige tarde demais.

## Próximo capítulo

[Pipeline de CI/CD com segurança →](06-pipeline-de-cicd-com-seguranca.md)
