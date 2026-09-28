FROM python:3.14-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt

FROM python:3.14-slim
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1
WORKDIR /app
COPY --from=builder /install /usr/local
RUN useradd --create-home --uid 10001 dashboard
USER dashboard
COPY app.py .
COPY templates/ templates/
EXPOSE 8501
HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8501/healthz')" || exit 1
# Server-rendered dashboard instead of `streamlit run` (see the docstring in app.py).
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8501"]
