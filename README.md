# 🎙️ Miniverso STT + Relatórios

Aplicação em **FastAPI** para:
- Transcrição de áudio (**STT**) com [faster-whisper](https://github.com/SYSTRAN/faster-whisper);
- Análise textual local via **Ollama** (LLM);
- Geração de **relatórios PDF**:
  - **Coletivo**: estatísticas agregadas de todos os áudios processados;
  - **Individual**: transcrição + análise LLM de um único áudio.

---

## 🚀 Funcionalidades

- **/transcrever**: endpoint POST para enviar arquivos `.wav` e receber transcrição + análise JSON.
- **/report/coletivo**: gera PDF agregado com métricas, gráficos e ranking.
- **/report/individual/{id}**: gera PDF individual com transcrição e análise LLM do áudio selecionado.

⚠️ **Somente arquivos WAV PCM 16-bit** são aceitos (mono ou estéreo, qualquer taxa; reamostrado para 16 kHz).

---

## 📂 Estrutura

```
.
├── stt_server.py                  # Servidor FastAPI
├── reports/
│   ├── audio_report.py            # Funções para gerar relatórios
│   ├── data/                      # Onde ficam os .jsonl das transcrições
│   │   └── stt_runs.jsonl
│   ├── output/                    # PDFs gerados
│   ├── static/
│   │   ├── charts/                # Gráficos gerados em PNG
│   │   └── miniverso/             # Logos (logo-horizontal.png, isotipo.png)
│   └── templates/
│       ├── audio_report_telemetry_like.html   # Template coletivo
│       └── audio_report_individual.html       # Template individual
```

---

## 🛠️ Requisitos

- Python 3.10+  
- [ffmpeg](https://ffmpeg.org) **não é necessário** (WAV puro apenas)
- Dependências Python:
```bash
pip install fastapi uvicorn faster-whisper numpy weasyprint jinja2 matplotlib python-multipart
```

Para análise LLM:
```bash
pip install ollama
```
e instale o [Ollama](https://ollama.ai) localmente.

---

## ▶️ Como rodar

1. Clone o projeto e entre na pasta.
2. Inicie o servidor:

```bash
uvicorn stt_server:app --host 0.0.0.0 --port 8001
```

3. Acesse no navegador:
```
http://localhost:8001/health
```

---

## 🎧 Enviar áudio

### Windows (PowerShell):
```powershell
curl.exe -X POST "http://<IP_SERVIDOR>:8001/transcrever" -F "file=@C:/Users/User/Desktop/audio.wav" -F "language=pt" -F "speaker=Maria"
```

### Linux/macOS:
```bash
curl -X POST "http://<IP_SERVIDOR>:8001/transcrever" -F "file=@/home/user/audio.wav" -F "language=pt" -F "speaker=Maria"
```

Retorno JSON (exemplo):
```json
{
  "ok": true,
  "id": "run-1757513801044",
  "text": "texto transcrito...",
  "analysis": {"summary":"...", "topics":["..."], "sentiment":"neutro"},
  "meta": {"model":"medium","device":"cuda","compute_type":"float16"}
}
```

---

## 📊 Relatórios

- **Coletivo** (todos os áudios):
  ```
  http://<IP_SERVIDOR>:8001/report/coletivo
  ```

- **Individual** (um áudio específico):
  ```
  http://<IP_SERVIDOR>:8001/report/individual/{id}
  ```

onde `{id}` é o valor retornado no JSON do `/transcrever`.

---

## ✨ Exemplos de PDF

- **Relatório coletivo**: métricas de duração, WPM, gráficos e ranking.
- **Relatório individual**: metadados do áudio, transcrição completa, análise LLM (resumo, tópicos, sentimento).

---

## 📌 Notas

- Os dados das transcrições ficam salvos em `reports/data/stt_runs.jsonl`.
- Os gráficos são gerados em `reports/static/charts/`.
- Os PDFs finais ficam em `reports/output/`.
- Caso a LLM retorne resposta inválida, o sistema aplica **fallback local** (resumo por heurística + tópicos por frequência).

---
