FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
RUN chown -R nobody:nogroup /app && \
    chmod -R 755 /app

USER nobody

ENV FLASK_APP=wsgi.py
ENV PYTHONUNBUFFERED=1
# The pod runs with readOnlyRootFilesystem: true, so __pycache__ writes into /app fail.
# Python tolerates that silently, but the failed attempts are pure noise on every import.
ENV PYTHONDONTWRITEBYTECODE=1

EXPOSE 5000

# No HEALTHCHECK on purpose. Upstream's called `curl -f http://localhost:5000/ping`, and
# python:3.9-slim ships no curl — the container reported `unhealthy` for its whole life. The
# deployment that consumes this image defines startup, readiness and liveness probes against
# that same /ping endpoint, which is where the check belongs.

CMD ["gunicorn", "--config", "app/config/gunicorn.py", "wsgi:app"] 