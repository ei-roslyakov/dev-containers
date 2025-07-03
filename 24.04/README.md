# 🐳 Ansible Dev Docker Image

A development container for running Ansible with AWS, SSH, Vault, and custom Python tools preconfigured.

---

## 🏗️ Build the Image

### For **amd64**:
```bash
docker buildx build --platform linux/amd64 -t dev-2404:latest .
```

### For **arm64** (e.g., Apple Silicon M1/M2):
```bash
docker buildx build --platform linux/arm64 -t dev-2404:latest .
```

> ℹ️ Ensure Docker BuildKit is enabled and `buildx` is set up:
> ```bash
> docker buildx create --use
> ```

---

## 🚀 Run the Container

Run the container with mounted volumes and Vault token:

```bash
docker run --rm -it \
  -v "$(pwd)":"/home/devuser/$(basename "$(pwd)")" \
  -v ~/.aws:/home/devuser/.aws \
  -v ~/.ssh:/home/devuser/.ssh \
  -v ~/.vault-token:/home/devuser/.vault-token \
  -v $HOME/.zsh_aliases:/home/devuser/.zsh_aliases \
  -v $HOME/.zsh_history:/home/devuser/.zsh_history \
  -e VAULT_TOKEN=$(cat ~/.vault-token) \
  -e AWS_PROFILE=$AWS_PROFILE \
  -w "/home/devuser/$(basename "$(pwd)")" \
  dev-2404:latest \
  zsh
```

### Mounts:
- `$(pwd)`: Mounts your current directory to `$(basename "$(pwd)")`.
- `~/.aws`: AWS CLI credentials.
- `~/.ssh`: SSH keys.
- `~/.vault-token`: Vault token file.
- `VAULT_TOKEN`: Environment variable to use with HashiCorp Vault.

> 🔐 Your token and keys are mounted read-only for safety.

---

## 🧰 Preinstalled Tools

- Python (via `pyenv`)
- pipenv
- AWS CLI
- kubectl
- Terraform + Terragrunt (via `tfswitch`/`tgswitch`)
