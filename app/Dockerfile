FROM cgr.dev/chainguard/python:latest-dev@sha256:6e0891cd4555d53adc2aee5d5610527681ba61e46629fbf4d143ad0e895700ad AS builder

USER 0
RUN apk add --no-cache postgresql-dev 

WORKDIR /app
COPY requirements.txt .
#RUN pip install --no-cache-dir -r requirements.txt --prefix=/install
# Use Chainguard Libraries index with credentials injected at build time
RUN --mount=type=secret,id=cg_username \
    --mount=type=secret,id=cg_password \
    pip install --no-cache-dir --require-hashes \
    --index-url https://$(cat /run/secrets/cg_username):$(cat /run/secrets/cg_password)@libraries.cgr.dev/python/simple/ \
    --extra-index-url https://pypi.org/simple/ \
    --prefix=/install \
    -r requirements.txt


FROM cgr.dev/chainguard/python:latest@sha256:df732a5b7ccde86d8d4a40873ac0c15b14ea5ef0dd143d2d4c42f10792db8e32
COPY --from=builder /install/lib/python3.14/site-packages /usr/lib/python3.14/site-packages
WORKDIR /app
COPY --chown=nonroot:nonroot . /app

ENV FLASK_APP=app.py
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1
#ENV FLASK_ENV=development
ENV FLASK_DEBUG=0

EXPOSE 8000
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')"
ENTRYPOINT ["python", "/app/app.py"]

