FROM mcr.microsoft.com/devcontainers/python:3

RUN apt-get update \
	&& apt-get install -y --no-install-recommends fish tmux curl ca-certificates \
	&& pip3 install --no-cache-dir uv \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/*