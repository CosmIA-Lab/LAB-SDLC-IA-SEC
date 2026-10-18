# Convenção de autoria das cartas

Regras para escrever ou revisar qualquer carta de recall, deste ponto ou dos próximos.
O objetivo é aprender e revisar — não só ser cobrado.

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
| Sequência do argumento | o ponto 1 inteiro, passo a passo, na ordem em que um puxa o próximo |
| Taxonomia | os quatro níveis: fundamento → princípio → técnica → mecanismo |
| Conceitos de base | os fundamentos e princípios que valem em todas as fases |
| As cinco fases | o ciclo de requisitos a deploy, com objetivo, princípios e técnicas de cada fase |

## Manutenção

As cartas são extraídas do `README.md` do ponto. Se o README mudar, as cartas
acompanham. Ao adicionar um ponto novo, defina seus baralhos com descrição antes de
escrever as cartas.
