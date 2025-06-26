FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1
ENV POWERLEVEL9K_INSTANT_PROMPT=quiet

# Tool Versions
ARG AWS_CLI_VERSION=2.15.0
ARG KUBECTL_VERSION=v1.30.1
ARG PYTHON_VERSION=3.11.9
ARG TERRAFORM_VERSION=1.11.4
ARG TERRAGRUNT_VERSION=0.80.2

# Install base packages and pyenv deps
RUN apt-get update && apt-get install -y \
    curl unzip wget git jq gnupg ca-certificates \
    openssh-client software-properties-common \
    build-essential libssl-dev zlib1g-dev libbz2-dev \
    libreadline-dev libsqlite3-dev libncursesw5-dev xz-utils \
    tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev \
    zsh fzf fonts-powerline \
    && rm -rf /var/lib/apt/lists/*

# Install AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${AWS_CLI_VERSION}.zip" -o awscliv2.zip && \
    unzip awscliv2.zip && ./aws/install && rm -rf aws awscliv2.zip

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && rm kubectl

# Install tfswitch and tgswitch
RUN curl -L https://raw.githubusercontent.com/warrensbox/terraform-switcher/release/install.sh | bash && \
    curl -L https://raw.githubusercontent.com/warrensbox/tgswitch/release/install.sh | bash

# Set PATHs
ENV PYENV_ROOT="/home/vscode/.pyenv"
ENV PATH="${PYENV_ROOT}/bin:${PYENV_ROOT}/shims:/home/vscode/.local/bin:/home/vscode/bin:${PATH}"

# Install pyenv + Python + Pipenv
RUN curl -fsSL https://pyenv.run | bash && \
    ${PYENV_ROOT}/bin/pyenv install ${PYTHON_VERSION} && \
    ${PYENV_ROOT}/bin/pyenv global ${PYTHON_VERSION} && \
    pip install --upgrade pip pipenv

# Install oh-my-zsh plugins and powerlevel10k theme, and configure ~/.zshrc
RUN export ZSH="/home/vscode/.oh-my-zsh" && \
    export ZSH_CUSTOM="${ZSH}/custom" && \
    git clone --depth=1 https://github.com/romkatv/powerlevel10k.git "${ZSH_CUSTOM}/themes/powerlevel10k" && \
    git clone https://github.com/zsh-users/zsh-autosuggestions "${ZSH_CUSTOM}/plugins/zsh-autosuggestions" && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting "${ZSH_CUSTOM}/plugins/zsh-syntax-highlighting" && \
    git clone https://github.com/unixorn/fzf-zsh-plugin.git "${ZSH_CUSTOM}/plugins/fzf-zsh-plugin" && \
    sed -i 's/^plugins=.*/plugins=(git zsh-autosuggestions zsh-syntax-highlighting fzf-zsh-plugin)/' /home/vscode/.zshrc && \
    sed -i 's/^ZSH_THEME=.*/ZSH_THEME="powerlevel10k\/powerlevel10k"/' /home/vscode/.zshrc && \
    echo '\n# Optional: fix fzf warning\n[ -f ~/.fzf/shell/key-bindings.zsh ] && source ~/.fzf/shell/key-bindings.zsh' >> /home/vscode/.zshrc && \
    echo 'POWERLEVEL9K_INSTANT_PROMPT=quiet' >> /home/vscode/.zshrc && \
    chown -R vscode:vscode /home/vscode

# Configure pyenv for login shells
RUN echo 'export PYENV_ROOT="$HOME/.pyenv"' >> /home/vscode/.bashrc && \
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> /home/vscode/.bashrc && \
    echo 'eval "$(pyenv init --path)"' >> /home/vscode/.bashrc && \
    echo 'eval "$(pyenv init -)"' >> /home/vscode/.bashrc && \
    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> /home/vscode/.zshrc && \
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> /home/vscode/.zshrc && \
    echo 'eval "$(pyenv init --path)"' >> /home/vscode/.zshrc && \
    echo 'eval "$(pyenv init -)"' >> /home/vscode/.zshrc

# Install Terraform and Terragrunt versions
RUN tfswitch ${TERRAFORM_VERSION} && \
    tgswitch ${TERRAGRUNT_VERSION}

USER vscode

# Install python requirements
COPY linux/ansible/requirements.txt /home/vscode/ansible-requirements.txt
RUN pip3 install -r /home/vscode/ansible-requirements.txt && \
    rm /home/vscode/ansible-requirements.txt
