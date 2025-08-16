# 🚀 Deploying Pixtral with vLLM in Docker and Exposing for Model Access

Large language models are compute-heavy, and deploying them efficiently requires optimized inference engines like **[vLLM](https://vllm.ai/)**. In this guide, we’ll walk through containerizing **Pixtral** using **Docker**, running it with vLLM, and exposing the model endpoint for external access.

---

## 📦 Prerequisites

- Docker installed (latest version recommended)
- NVIDIA GPU with CUDA drivers (if running with GPU acceleration)
- Model weights available (Pixtral Hugging Face repo or local)

---

## 🛠 Step 1: Create a Dockerfile

```dockerfile
FROM nvidia/cuda:12.2.2-runtime-ubuntu22.04

# System dependencies
RUN apt-get update && apt-get install -y \
    git wget curl python3 python3-pip && \
    rm -rf /var/lib/apt/lists/*

# Install vLLM
RUN pip3 install --upgrade pip
RUN pip3 install vllm

# Set working directory
WORKDIR /app

# Expose port for API
EXPOSE 8000

# Default command (can be overridden)
CMD ["bash"]
```

---

## ⚙️ Step 2: Build the Docker Image

```bash
docker build -t pixtral-vllm .
```

---

## 📂 Step 3: Run the Container with Model Access

You can start the vLLM server inside the container to serve **Pixtral**.

```bash
docker run --gpus all -it -p 8000:8000 pixtral-vllm \
  python3 -m vllm.entrypoints.openai.api_server \
  --model <huggingface_repo_or_local_path_to_pixtral>
```

- Replace `<huggingface_repo_or_local_path_to_pixtral>` with your model location.
- The container will serve an **OpenAI-compatible API** on port `8000`.

---

## 🌐 Step 4: Test the API Endpoint

After running the container, test with `curl`:

```bash
curl http://localhost:8000/v1/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "pixtral", "prompt": "Hello Pixtral!"}'
```

Expected response:
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

## 📡 Step 5: Expose for External Access

If you want to make the API accessible from outside the host:
- Map the container port `8000` to the host (`-p 8000:8000`).
- Ensure firewall/security group allows inbound traffic on `8000`.

For production, consider:
- Reverse proxy with **NGINX** or **Traefik**
- Adding **authentication** before exposing publicly

---

## ✅ Conclusion

You now have **Pixtral deployed with vLLM inside Docker**, exposed via an **OpenAI-compatible API**. This setup allows you to scale inference workloads efficiently, while keeping deployment portable.

---

### 🔗 References
- [vLLM Documentation](https://docs.vllm.ai/)
- [Pixtral Model (Hugging Face)](https://huggingface.co/)
