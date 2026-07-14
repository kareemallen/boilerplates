# Devcontainer

This folder contains the development container configuration for the repository.

The container is built from `mcr.microsoft.com/devcontainers/python:3` and adds a small set of tools for local development and infrastructure work.

## Base image

- Python devcontainer base image
- Custom Dockerfile build located in [Dockerfile](Dockerfile)

## Installed tools

The Dockerfile installs these packages inside the container:

- `fish`
- `tmux`
- `curl`
- `ca-certificates`
- `uv`

## Devcontainer features

The devcontainer also enables these features:

- `act` for running GitHub Actions locally
- Docker-in-Docker support
- `k3d` for lightweight local Kubernetes clusters
- `k9s` for terminal-based Kubernetes cluster management

## Runtime configuration

- `remoteUser` is set to `vscode`
- A persistent volume is mounted at `/root`
- The VS Code server state is stored in a volume at `/root/.vscode-server`

## VS Code extensions

The container pre-installs these extensions:

- `ms-vscode.cpptools`
- `ms-vscode.cmake-tools`

## Notes

The container is intended to provide a Python-focused shell environment with a few common tools for automation, terminal workflows, and Kubernetes-related tasks.