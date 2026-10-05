FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY codinzhub-graph-rag.py .

EXPOSE 8501

CMD ["streamlit", "run", "codinzhub-graph-rag.py", "--server.address", "0.0.0.0"]
