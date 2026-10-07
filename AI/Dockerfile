FROM python:3.11-slim

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    TOKENIZERS_PARALLELISM=false \
    HF_HOME=/tmp/huggingface

RUN groupadd --system --gid 10001 app \
    && useradd --system --uid 10001 --gid app --home-dir /app app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY ai_service_api.py ./
COPY src/ ./src/
COPY knowledge/ ./knowledge/

USER 10001:10001
EXPOSE 5000

CMD ["uvicorn", "ai_service_api:app", "--host", "0.0.0.0", "--port", "5000"]
