# build-llama-cpp

A local workspace for building llama.cpp and running local GGUF models.

## Checkout and build

```sh
git -C llama.cpp checkout b9555
task release:configure
task release:build
```

## Models

Models are not committed to this repository. Put GGUF files under `./model/`.

Tested models:

- [gemma-4-12B-it-Q4_K_M.gguf](https://huggingface.co/ggml-org/gemma-4-12B-it-GGUF/blob/main/gemma-4-12B-it-Q4_K_M.gguf)
  - Variant: instruction-tuned
  - Quantization: `Q4_K_M`
  - SHA256: `1278394b693672ac2799eadc9a83fd98259a6a88a40acfb1dcaa6c6fc895a606`
- [gemma-4-12B-it-Q8_0.gguf](https://huggingface.co/ggml-org/gemma-4-12B-it-GGUF/blob/main/gemma-4-12B-it-Q8_0.gguf)
  - Variant: instruction-tuned
  - Quantization: `Q8_0`
  - SHA256: `047dae1d7894b9de8f08141e841544e007243290c02df8b39872991d1940c795`

Check a downloaded model hash:

```sh
shasum -a 256 model/<filename>.gguf
```

## Run with llama.cpp

After downloading a model:

```sh
./build/bin/llama-cli -m ./model/gemma-4-12B-it-Q4_K_M.gguf -p "Hello"
```

## Serving models with llama-server

`llama-server` exposes an OpenAI-compatible API.
Open WebUI can connect to it with `http://host.docker.internal:<port>/v1`.

Important options:

- `--host 0.0.0.0`: listen on all interfaces so Docker containers can reach the host server.
- `--port <port>`: server port used in the Open WebUI connection URL.
- `--model <path>` / `-m <path>`: local GGUF model file.
- `--hf-repo <repo>:<quant>` / `-hf <repo>:<quant>`: download and run a GGUF model from Hugging Face.
- `--alias <name>`: model id returned by `/v1/models`; this is the name shown in Open WebUI.
- `--ctx-size <tokens>`: context window size for chat requests.
- `--gpu-layers all`: offload all possible layers to GPU/Metal.
- `--jinja`: use the model chat template; important for chat, reasoning, and tool-use behavior.
- `--reasoning on`, `--reasoning-budget <tokens>`, `--reasoning-format deepseek-legacy`: thinking/reasoning settings when the model supports them.

Example:

```sh
./build/bin/llama-server \
  --model ./model/gemma-4-12B-it-Q4_K_M.gguf \
  --alias gemma-4-12b-it-q4 \
  --host 0.0.0.0 \
  --port 8080 \
  --ctx-size 8192 \
  --gpu-layers all
```

## Run Open Web UI

```sh
cp .env.example .env
task open-web-ui:up:detach
task open-web-ui:logs
```

Open [http://localhost:3000](http://localhost:3000).