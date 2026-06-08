# build-llama-cpp

A local workspace for building llama.cpp and running local GGUF models.

## Checkout and build

```sh
git -C llama.cpp checkout b9555
task release:configure
task release:build
```

## Server tasks

Models are started with `llama-server -hf ...`, so model files do not need to be committed or placed under this repository. On first run, llama.cpp downloads the GGUF files into its cache.

- `task server:lfm25-jp`
  - Model: `LiquidAI/LFM2.5-1.2B-JP-202606-GGUF:Q4_K_M`
  - Open WebUI URL: `http://host.docker.internal:8081/v1`
- `task server:gemma4-e2b-qat`
  - Model: `google/gemma-4-E2B-it-qat-q4_0-gguf:Q4_0`
  - Open WebUI URL: `http://host.docker.internal:8082/v1`
- `task server:gemma4-e4b-qat`
  - Model: `google/gemma-4-E4B-it-qat-q4_0-gguf:Q4_0`
  - Open WebUI URL: `http://host.docker.internal:8083/v1`
- `task server:gemma4-12b-qat`
  - Model: `google/gemma-4-12B-it-qat-q4_0-gguf:Q4_0`
  - Open WebUI URL: `http://host.docker.internal:8084/v1`
- `task server:gemma4-26b-a4b-qat`
  - Model: `google/gemma-4-26B-A4B-it-qat-q4_0-gguf:Q4_0`
  - Open WebUI URL: `http://host.docker.internal:8085/v1`

## Serving models with llama-server

`llama-server` exposes an OpenAI-compatible API.
Open WebUI can connect to it with `http://host.docker.internal:<port>/v1`.

Important options:

- `--host 0.0.0.0`: listen on all interfaces so Docker containers can reach the host server.
- `--port <port>`: server port used in the Open WebUI connection URL.
- `--hf-repo <repo>:<quant>` / `-hf <repo>:<quant>`: download and run a GGUF model from Hugging Face.
- `--model <path>` / `-m <path>`: local GGUF model file, if using a manually downloaded model.
- `--alias <name>`: model id returned by `/v1/models`; this is the name shown in Open WebUI.
- `--ctx-size <tokens>`: context window size for chat requests.
- `--gpu-layers all`: offload all possible layers to GPU/Metal.
- `--jinja`: use the model chat template; important for chat, reasoning, and tool-use behavior.
- `--reasoning on`, `--reasoning-budget <tokens>`, `--reasoning-format deepseek-legacy`: thinking/reasoning settings when the model supports them.

Example:

```sh
task server:lfm25-jp
```

## Run Open Web UI

```sh
cp .env.example .env
task open-web-ui:up:detach
task open-web-ui:logs
```

Open [http://localhost:3000](http://localhost:3000).
