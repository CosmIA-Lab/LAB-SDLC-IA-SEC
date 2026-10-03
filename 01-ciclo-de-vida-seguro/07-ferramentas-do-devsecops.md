# 7. Ferramentas do DevSecOps

Cada letra do DevSecOps tem ferramentas correspondentes.

## SAST — Static Application Security Testing

Analisa o código-fonte sem executá-lo. Encontra padrões de falha
conhecidos: SQL injection, buffer overflow, uso de funções inseguras.
Exemplos: Semgrep, SonarQube, CodeQL.

## DAST — Dynamic Application Security Testing

Testa a aplicação rodando, como um atacante faria. Envia requisições
maliciosas e observa as respostas. Exemplos: OWASP ZAP, Burp Suite.

## SCA — Software Composition Analysis

Verifica as dependências de terceiros. Bibliotecas com vulnerabilidades
conhecidas (CVEs) são sinalizadas. Exemplos: Dependabot, Snyk, Trivy.

## Fuzzing

Envia entradas aleatórias ou malformadas para o programa e observa
se ele quebra. Bom para encontrar falhas de memória e parsing.

## Secrets scanning

Procura credenciais vazadas no código: senhas, tokens, chaves de API.
Exemplos: GitGuardian, gitleaks, trufflehog.

## Próximo capítulo

[SDLC seguro vs DevSecOps →](08-sdlc-seguro-vs-devsecops.md)
