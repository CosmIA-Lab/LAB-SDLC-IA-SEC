# Ponto 3 — Modelagem de ameaças e análise de superfície de ataque

## Objetivo

Entender o que é modelagem de ameaças, como identificar ameaças de forma sistemática e como analisar a superfície de ataque de um sistema.

---

## 1. O que é modelagem de ameaças

Modelagem de ameaças é o processo de identificar, documentar e priorizar as ameaças que podem afetar um sistema, antes de ele ser construído ou enquanto é construído.

A ideia é responder quatro perguntas:

1. O que estamos construindo?
2. O que pode dar errado?
3. O que vamos fazer sobre isso?
4. Tivemos sucesso?

É uma atividade da fase de requisitos e design do SDLC seguro: quanto mais cedo as ameaças são identificadas, mais barato é corrigir.

---

## 2. Por que fazer modelagem de ameaças

- Encontra falhas de design antes do código existir.
- Prioriza o que proteger: nem tudo tem o mesmo valor.
- Cria linguagem comum entre desenvolvedores, segurança e negócio.
- Documenta decisões de segurança para auditoria.

---

## 3. STRIDE

STRIDE é a técnica mais usada para modelagem de ameaças. Cada letra é uma categoria de ameaça:

| Letra | Ameaça | Significado |
|---|---|---|
| **S** | Spoofing | Fingir ser outra pessoa ou sistema |
| **T** | Tampering | Alterar dados sem autorização |
| **R** | Repudiation | Negar ter feito uma ação |
| **I** | Information Disclosure | Expor dados que deveriam ser privados |
| **D** | Denial of Service | Tornar o sistema indisponível |
| **E** | Elevation of Privilege | Ganhar acesso além do permitido |

### Como aplicar

1. Desenhe o diagrama de fluxo de dados do sistema.
2. Para cada elemento (processo, dado, armazenamento, fluxo), pergunte: qual ameaça STRIDE se aplica?
3. Documente cada ameaça encontrada com mitigação.

### Exemplos por letra

- **Spoofing:** atacante usa credenciais roubadas para acessar conta de cliente.
- **Tampering:** atacante altera valor de um pagamento em trânsito.
- **Repudiation:** usuário nega ter feito uma transferência.
- **Information Disclosure:** API retorna dados de outros clientes sem autorização.
- **Denial of Service:** requisições em massa derrubam o serviço.
- **Elevation of Privilege:** usuário comum executa ações de administrador.

---

## 4. Superfície de ataque

A superfície de ataque é o conjunto de todos os pontos por onde um atacante pode tentar entrar ou causar dano: interfaces, APIs, formulários, portas, dependências, canais de comunicação.

### Como analisar

1. Liste todos os pontos de entrada do sistema.
2. Para cada ponto, identifique o que ele expõe e quem pode acessá-lo.
3. Avalie o risco de cada ponto: probabilidade vezes impacto.
4. Priorize a mitigação dos pontos de maior risco.

### Exemplos de pontos de entrada

- Endpoints de API
- Formulários web
- Uploads de arquivo
- Integrações com sistemas externos
- Dependências de terceiros
- Canais de autenticação

---

## 5. Técnicas para memorizar

| Conceito | Definição |
|---|---|
| Modelagem de ameaças | Identificar, documentar e priorizar ameaças |
| STRIDE | Seis categorias: Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege |
| Superfície de ataque | Todos os pontos de entrada do sistema |
| Diagrama de fluxo de dados | Base para aplicar STRIDE |

---

## Referências

- Microsoft SDL Threat Modeling — learn.microsoft.com (procure "Threat Modeling")
- OWASP Threat Modeling — owasp.org (procure "Threat Modeling")
- NIST SSDF — nist.gov/publications/sp-800-218
