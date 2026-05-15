FROM nvidia/cuda:12.8.1-cudnn-runtime-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1
ENV MODEL_CACHE_SIZE=5
ENV ACTIVE_MODELS=Qwen/Qwen3-TTS-12Hz-1.7B-Base,Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice
ENV HOME=/tmp
ENV TORCHINDUCTOR_CACHE_DIR=/tmp/torch_inductor
# Well-known mount point for HuggingFace hub cache.
# Offline usage: mount host HF cache here and set HF_HUB_OFFLINE=1
#   docker run -v ~/.cache/huggingface/hub:/models -e HF_HUB_OFFLINE=1 ...
ENV HF_HUB_CACHE=/models

RUN sed -i 's|http://archive.ubuntu.com|http://mirrors.aliyun.com|g; s|http://security.ubuntu.com|http://mirrors.aliyun.com|g' /etc/apt/sources.list \
    && apt-get update && apt-get install -y --no-install-recommends \
    python3 python3-pip python3-venv \
    git ffmpeg libsndfile1 sox \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY . /app

RUN python3 -m pip install --no-cache-dir --upgrade pip \
    && python3 -m pip install --no-cache-dir --index-url https://download.pytorch.org/whl/cu128 torch torchaudio \
    && python3 -m pip install --no-cache-dir ".[demo]" \
    && python3 -m pip install --no-cache-dir -r demo/requirements.txt

EXPOSE 7860
CMD ["python3", "demo/server.py", "--host", "0.0.0.0", "--model", "Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice"]
