FROM nvidia/cuda:11.8.0-runtime-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Install Python and pip
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    python3-setuptools \
    python3-dev \
    git \
    curl \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Optional: make pip point to python3
RUN ln -sf /usr/bin/python3 /usr/bin/python && \
    ln -sf /usr/bin/pip3 /usr/bin/pip


WORKDIR /workspace
COPY requirements.txt .

# Install all packages cleanly (no preinstalled conflicts)
RUN pip install --no-cache-dir --verbose -r requirements.txt

COPY 05_claudia10_metrics.py classification.py
COPY data ./data

CMD ["python", "classification.py"]

