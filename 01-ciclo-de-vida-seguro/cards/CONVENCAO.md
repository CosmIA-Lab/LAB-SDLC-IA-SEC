# Convenção de autoria do estudo interativo

Regras para escrever ou revisar o estudo de um ponto, deste ou dos próximos.
O objetivo é aprender e revisar — não só ser cobrado.

Cada ponto tem um `cards/index.html` com dois modos no mesmo app:

- **Trilha guiada** (`STEPS`): os passos na ordem do `README.md` do ponto, cada
  um com explicação e um exercício. É o modo de aprender a primeira vez. Tipos de
  exercício: múltipla escolha (`mc`), ordenar (`order`), classificar (`sort`),
  curva de custo (`cost`) e camadas de defesa (`layers`). Todo exercício ensina
  no feedback, nunca só pontua.
- **Cartas de revisão** (`CARDS`/`DECKS`): retrieval practice para fixar depois.

As duas superfícies saem do `README.md` do ponto e seguem as mesmas regras de
conteúdo abaixo. O conteúdo é conteudista: ensina o que a coisa é, nunca cobra
meta-pergunta sobre o fluxo de estudo ou a convenção.

## Schema de uma carta

```js
{
  d:        "fases",                 // baralho (agrupamento e filtro)
  topic:    "Fase · Testes",         // tópico do ponto a que a carta pertence
  objective:"distinguir as 4 análises", // o que esta carta treina
  q:        "...",                   // pergunta (frente)
  a:        "...",                   // resposta (verso), curta
}
```

Todo card mostra no topo o **tópico** e o **objetivo**. Isso situa a carta antes de
você responder.

## Regras

1. **Literal, nunca metáfora.** Se o leitor precisa adivinhar uma analogia para
   entender, está errado. Nome de baralho, tópico e pergunta dizem o que a coisa é.
   (Foi por isso que "Cadeia" virou "Sequência do argumento" e "Escada" virou
   "Taxonomia".)
2. **Sigla sempre expandida na primeira aparição**, na própria frente da carta:
   "SCA (análise de dependências)", não "SCA" sozinho. Quem ainda está aprendendo
   não deve travar na sigla.
3. **Uma ideia por carta.** Se a resposta tem duas ideias independentes, são duas
   cartas.
4. **Resposta curta.** O verso ensina o mínimo para fixar, não é um parágrafo de
   README. Negrito só no que precisa ser lembrado.
5. **Fonte nomeada quando for técnica** (quem, ano): "Inspeção formal de código —
   Fagan, 1976". Sem nome e fonte, não é técnica.
6. **Baralho com descrição.** Todo baralho carrega um subtítulo de uma linha, visível
   na interface (legenda) — nunca um rótulo opaco.

## Baralhos atuais (ponto 1)

| Baralho | Descrição |
|---|---|
| Pushing left | por que o momento da descoberta define o custo, e a diferença entre falha e bug |
| Conceitos de base | os fundamentos e princípios que valem em todas as fases |
| Requisitos | fase 1: ativos, classificação, requisito verificável e técnicas de elicitação |
| Design | fase 2: arquitetura segura antes do código, modelagem de ameaças |
| Código | fase 3: codificação segura, code review, SAST no commit |
| Testes | fase 4: os alvos, a evidência e as técnicas de teste |
| Deploy e descontinuação | fase 5 e a fase extra: produção monitorada, resposta, encerramento |
| Ciclo e DevSecOps | o ciclo completo, shift-left e shift-right, pipeline e ferramentas |

## Manutenção

As cartas são extraídas do `README.md` do ponto. Se o README mudar, as cartas
acompanham. Ao adicionar um ponto novo, defina seus baralhos com descrição antes de
escrever as cartas.
