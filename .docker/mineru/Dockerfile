# Full MinerU local API image for GPU/VLM-capable parsing.
# The base image should already exist locally. If Docker cannot pull it,
# pre-pull and tag it with:
#   docker pull vllm/vllm-openai:v0.11.2
#   docker tag vllm/vllm-openai:v0.11.2 docker.m.daocloud.io/vllm/vllm-openai:v0.11.2
FROM vllm/vllm-openai:v0.10.2

ENV PYTHONUNBUFFERED=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# vLLM image is Ubuntu 22.04. Use a China mirror for apt packages, while
# leaving CUDA/deadsnakes source files unchanged.
RUN sed -i \
        -e 's|http://archive.ubuntu.com/ubuntu|http://mirrors.tuna.tsinghua.edu.cn/ubuntu|g' \
        -e 's|http://security.ubuntu.com/ubuntu|http://mirrors.tuna.tsinghua.edu.cn/ubuntu|g' \
        /etc/apt/sources.list && \
    apt-get update -o Acquire::Retries=5 && \
    apt-get install -y --no-install-recommends -o Acquire::Retries=5 \
        fonts-noto-core \
        fonts-noto-cjk \
        fontconfig \
        libgl1 && \
    fc-cache -fv && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN python3 -m pip install -U "mineru[core]>=3.0.0" \
        -i https://mirrors.aliyun.com/pypi/simple --break-system-packages && \
    python3 -m pip cache purge

# Download all model weights needed by hybrid/vlm/pipeline backends.
RUN MINERU_MODEL_SOURCE=modelscope mineru-models-download -s modelscope -m all

ENTRYPOINT ["/bin/bash", "-c", "export MINERU_MODEL_SOURCE=local && exec \"$@\"", "--"]
