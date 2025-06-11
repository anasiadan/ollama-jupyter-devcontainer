FROM mcr.microsoft.com/devcontainers/python:1-3.12-bullseye

WORKDIR /workspace

COPY requirements.txt .
RUN pip install -r requirements.txt

CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]