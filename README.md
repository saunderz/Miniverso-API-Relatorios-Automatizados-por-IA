# Miniverso STT + Relatórios

**Descrição**  
Aplicação robusta em **FastAPI** com foco em transcrição automática de arquivos de áudio em **português brasileiro (pt‑BR)**. Inclui detecção de silêncio, segmentação, geração de timestamps, análises via LLM local (Ollama) e geração de relatórios PDF tanto coletivos quanto individuais.

---

## Funcionalidades

- **/transcrever** (POST): aceita `.wav` (PCM 16‑bit), envia transcrição e análise JSON (texto, resumo, tópicos, sentimento, metadados).
- **/report/coletivo**: gera PDF com métricas agregadas, gráficos e ranking de todos os áudios processados.
- **/report/individual/{id}**: gera PDF com transcrição completa e análise LLM de um único áudio.

---

## Arquitetura & Fluxo

graph TD
  A([🎤 Arquivo .wav]) --> B{{🚀 API FastAPI}}
  B --> C([📝 STT - faster-whisper])
  C --> D([🤖 Análise LLM - Ollama])
  C --> E[(📂 Logs JSONL)]
  D --> F[[📑 Templates Jinja2 + WeasyPrint]]
  F --> G([📊 Relatórios PDF])

**Componentes principais**:
- `stt_server.py`: servidor FastAPI.
- `audio_report.py`: lógica de geração de relatórios.
- `reports/data/stt_runs.jsonl`: base de transcrições.
- `reports/static/charts/`: gráficos em PNG.
- `reports/templates/`: templates HTML para geração dos PDFs.
- `reports/output/`: relatórios finais em PDF.

---

## Tecnologias & Dependências

- **Backend**: Python ≥ 3.10, FastAPI, Uvicorn
- **Processamento de áudio**: faster‑whisper (com suporte a GPU), SpeechRecognition, Pydub
- **Análise LLM local**: Ollama
- **Geração de relatórios**: Jinja2, WeasyPrint, Matplotlib
- **Outras dependências**: numpy, python‑multipart

**Ambiente**:
- Formato suportado: WAV PCM 16‑bit (mono/estéreo), reamostrado para 16 kHz.
- **Observe**: `ffmpeg` não é exigido.

---

## Demonstração (exemplos de uso)

### Iniciar servidor
```bash
uvicorn stt_server:app --host 0.0.0.0 --port 8001
```

### Verificar saúde
Abra no navegador:
```
http://localhost:8001/health
```

### Transcrever áudio (Windows - PowerShell)
```powershell
curl.exe -X POST "http://<IP_SERVIDOR>:8001/transcrever" -F "file=@C:/caminho/audio.wav" -F "language=pt" -F "speaker=Maria"
```

### Transcrever áudio (Linux/macOS)
```bash
curl -X POST "http://<IP_SERVIDOR>:8001/transcrever"   -F "file=@/caminho/audio.wav"   -F "language=pt"   -F "speaker=Maria"
```

**Resposta JSON esperada**:
```json
{
  "ok": true,
  "id": "run‑1234567890",
  "text": "...",
  "analysis": {
    "summary": "...",
    "topics": ["..."],
    "sentiment": "neutro"
  },
  "meta": {
    "model": "medium",
    "device": "cuda",
    "compute_type": "float16"
  }
}
```

### Acessar relatórios (via navegador)
- **Coletivo**: `http://<IP_SERVIDOR>:8001/report/coletivo`
- **Individual**: `http://<IP_SERVIDOR>:8001/report/individual/{id}`

---

## Exemplo Visual (opcional)

*(Aqui você pode inserir – idealmente como image link ou GIF – capturas dos PDFs gerados: gráficos, ranking, transcrição com análise LLM etc.)*

---

## Resultados e Métricas

- Demonstra tempo médio de resposta da transcrição.
- Precisão da transcrição.
- Quantidade de áudios processados.
- Insights extraídos: sentimento predominante, temas recorrentes, volume de palavras por minuto (WPM), etc.

---

## Aplicações & Benefícios

- **Alta escalabilidade**: processamento local ou com GPU.
- **Apresentações analíticas**: PDFs prontos para relatórios corporativos ou acadêmicos.
- **Modularidade**: arquitetura separada (STT, LLM, relatórios).
- **Privacidade e Performance**: solução local sem depender de APIs externas.

---

## Próximos passos sugeridos

- Integrar modelos Whisper mais recentes ou especialização em sotaques regionais.
- Adotar OAuth/JWT para segurança de endpoints.
- Front-end web para interface visual (upload de áudio, visualização dos relatórios).
- Exportar métricas para dashboards (Grafana / Kibana).

---

## Contato

**Autor**: Luã Saunders — Engenheiro da Computação  
**Contato**: [e-mail, LinkedIn, etc.]  
**Status**: Projeto demonstrativo / R&D — código-fonte disponível sob solicitação ou protegido por NDA.

---

**Licença**: MIT (ou outra, conforme sua preferência)
