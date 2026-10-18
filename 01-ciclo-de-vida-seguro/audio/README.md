# Áudio — Ponto 1

Narração para escuta passiva (carro, caminhada). Segue a sequência do argumento,
em voz de dissertação.

- `ponto-1.mp3` — áudio pronto (~6 min, voz neural pt-BR).
- `roteiro.md` — o roteiro legível, com títulos.
- `narracao.txt` — texto plano enviado ao sintetizador.

Voz: Piper (neural), modelo `pt_BR-faber-medium`. O modelo (`voices/`) e o WAV
intermediário não são versionados — veja `.gitignore`.

## Regerar o áudio

1. Extrair a narração do roteiro:

```bash
cd 01-ciclo-de-vida-seguro/audio
python3 - <<'PY'
txt=open('roteiro.md',encoding='utf-8').read()
body=txt.split('---',1)[1]
paras=[l.strip() for l in body.split('\n') if l.strip() and not l.strip().startswith('#')]
open('narracao.txt','w',encoding='utf-8').write('\n\n'.join(paras))
PY
```

2. Baixar a voz (uma vez):

```bash
mkdir -p voices && cd voices
BASE="https://huggingface.co/rhasspy/piper-voices/resolve/main/pt/pt_BR/faber/medium"
curl -sL -O "$BASE/pt_BR-faber-medium.onnx"
curl -sL -O "$BASE/pt_BR-faber-medium.onnx.json"
cd ..
```

3. Sintetizar e encodar em mp3:

```bash
uv run --with piper-tts --with lameenc python - <<'PY'
import wave, lameenc
from piper import PiperVoice, SynthesisConfig
# velocidade: escala onde 50 = neutro (length_scale 1.0); maior = mais rápido
speed = 60
length_scale = round(1.0 - (speed-50)*0.004, 3)
v = PiperVoice.load("voices/pt_BR-faber-medium.onnx")
txt = open("narracao.txt", encoding="utf-8").read()
cfg = SynthesisConfig(length_scale=length_scale, noise_scale=0.9, noise_w_scale=1.3)
with wave.open("ponto-1.wav","wb") as wf:
    v.synthesize_wav(txt, wf, syn_config=cfg)
w = wave.open("ponto-1.wav","rb"); pcm = w.readframes(w.getnframes())
enc = lameenc.Encoder(); enc.set_bit_rate(96); enc.set_in_sample_rate(w.getframerate())
enc.set_channels(w.getnchannels()); enc.set_quality(2)
open("ponto-1.mp3","wb").write(enc.encode(pcm)+enc.flush())
PY
```

Parâmetros de voz:
- `length_scale` — ritmo (menor = mais rápido). `speed=60` → `0.96`.
- `noise_scale` / `noise_w_scale` — variação de pitch e de duração; aumentam a
  entonação (aqui `0.9` e `1.3`).
