# Áudio — Ponto 1

Narração para escuta passiva (carro, caminhada). Segue a sequência do argumento,
em voz de dissertação.

- `ponto-1.mp3` — áudio pronto (~5,5 min, voz pt-BR).
- `roteiro.md` — o roteiro legível, com títulos.
- `narracao.txt` — texto plano enviado ao sintetizador.

## Regerar o áudio

Se o roteiro mudar, regere:

```bash
cd 01-ciclo-de-vida-seguro/audio
python3 - <<'PY'
txt=open('roteiro.md',encoding='utf-8').read()
body=txt.split('---',1)[1]
paras=[l.strip() for l in body.split('\n') if l.strip() and not l.strip().startswith('#')]
open('narracao.txt','w',encoding='utf-8').write('\n\n'.join(paras))
PY
uv run --with gtts python -c "from gtts import gTTS; gTTS(open('narracao.txt',encoding='utf-8').read(), lang='pt', tld='com.br').save('ponto-1.mp3')"
```

Alternativa 100% local (voz robótica), sem internet:

```bash
espeak-ng -v pt-br -s 150 -f narracao.txt -w ponto-1.wav
```
