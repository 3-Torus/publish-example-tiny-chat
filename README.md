# publish-example-tiny-chat

An **example app for publishing on 3-Torus** ("Publish your app" in the
3-Torus portal). It is a small chat model you can talk to in the browser, and
it runs on a CPU: no GPU is needed.

- Server: [llama.cpp](https://github.com/ggml-org/llama.cpp)'s `llama-server`,
  image `ghcr.io/ggml-org/llama.cpp:server-b11783`, pinned by digest.
- Model: [SmolLM2-135M-Instruct](https://huggingface.co/HuggingFaceTB/SmolLM2-135M-Instruct)
  (Apache-2.0), the 8-bit GGUF file `SmolLM2-135M-Instruct-Q8_0.gguf`
  (145 MB) from [bartowski/SmolLM2-135M-Instruct-GGUF](https://huggingface.co/bartowski/SmolLM2-135M-Instruct-GGUF).
  It is downloaded from Hugging Face each time the app starts.

It is a *classic* repository on purpose: it has no `.3torus/app.yaml`. 3-Torus
reads `docker-compose.yml`, suggests an app file from it, and the publisher
fills in the rest in a short form. The app is set up only through the image,
the port and environment variables; there is no command line to change.

## Run it yourself

```bash
docker compose up
```

Then open http://localhost:8080 for the chat page. `GET /health` answers 200
once the model is loaded (503 while it loads), and `/v1/chat/completions` is
an OpenAI-compatible API:

```bash
curl -s http://localhost:8080/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{"messages":[{"role":"user","content":"Hello!"}]}'
```

## Settings

| Variable | Value here | What it does |
|---|---|---|
| `LLAMA_ARG_HF_REPO` | `bartowski/SmolLM2-135M-Instruct-GGUF` | Hugging Face repository to download the model from |
| `LLAMA_ARG_HF_FILE` | `SmolLM2-135M-Instruct-Q8_0.gguf` | the GGUF file in that repository |
| `LLAMA_ARG_HOST` | `0.0.0.0` | address to listen on |
| `LLAMA_ARG_PORT` | `8080` | port to listen on |
| `LLAMA_ARG_CTX_SIZE` | `4096` | context size in tokens, shared by the slots |
| `LLAMA_ARG_N_PARALLEL` | `2` | how many conversations it answers at once |

A model this small is a demonstration, not an assistant: it answers short,
simple questions and gets longer ones wrong. Nothing here is a secret, and
nothing ever should be.
