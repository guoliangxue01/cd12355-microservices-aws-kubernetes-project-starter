FROM python:3.10-slim-bullseye

WORKDIR /app

COPY . .

RUN apt-get update \
    && apt-get install -y build-essential libpq-dev \
    && pip install --no-cache-dir --upgrade pip setuptools wheel \
    && pip install --no-cache-dir -r requirements.txt

ENV DB_USERNAME=udacity \
    DB_PASSWORD=udacity \
    DB_HOST=localhost \
    DB_PORT=5433 \
    DB_NAME=udacitydatabase

CMD ["python", "app.py"]
