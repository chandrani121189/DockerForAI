# 🐳 Pixtral + vLLM in Docker

Deploy **Pixtral** with [vLLM](https://vllm.ai/) inside Docker and expose it as an **OpenAI-compatible API** for scalable inference.  
This repo contains a Dockerfile and usage instructions to quickly get started.

---

## 🚀 Features
- Run **Pixtral** using **vLLM** for optimized inference
- Containerized with **Docker** for portability
- GPU acceleration with **NVIDIA CUDA**
- Exposes **OpenAI API-compatible endpoint** (`/v1/completions`)
- Easy to test with `curl` or client SDKs

---

## 📦 Prerequisites
- [Docker](https://docs.docker.com/get-docker/) (latest version)
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) (for GPU support)
- Model weights available (Hugging Face repo or local path)

---

## 🛠 Build the Docker Image

Clone this repository and build the image:

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
docker build -t pixtral-vllm .
```

---

## ⚙️ Run the Container

Run Pixtral with GPU support and expose port `8000`:

```bash
docker run --gpus all -it -p 8000:8000 pixtral-vllm   python3 -m vllm.entrypoints.openai.api_server   --model <huggingface_repo_or_local_path_to_pixtral>
```

Replace `<huggingface_repo_or_local_path_to_pixtral>` with:
- Hugging Face model repo (e.g., `huggingface/pixtral-model`)
- Local path to your Pixtral weights

---

## 🌐 Test the API

Send a test request with `curl`:

```bash
curl http://localhost:8000/v1/completions   -H "Content-Type: application/json"   -d '{"model": "pixtral", "prompt": "Hello Pixtral!"}'
```

Example response:
```json
{
  "id": "cmpl-1234",
  "object": "text_completion",
  "choices": [
    {"text": "Hello there!"}
  ]
}
```

---

## 📡 Exposing Publicly

If deploying on a server:
- Ensure firewall/security groups allow inbound traffic on port `8000`
- For production, use a reverse proxy (NGINX / Traefik) + authentication

---

## 🧩 Roadmap
- [ ] Add **Docker Compose** support
- [ ] Provide **Helm chart** for Kubernetes
- [ ] Auto-build Docker images via **GitHub Actions**

---

## 📜 License
MIT License. See [LICENSE](LICENSE) for details.

---

## 🔗 References
- [vLLM Documentation](https://docs.vllm.ai/)
- [Pixtral on Hugging Face](https://huggingface.co/)
