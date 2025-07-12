FROM ubuntu:24.04

# Set environment
ENV DEBIAN_FRONTEND=noninteractive \
    PIP_BREAK_SYSTEM_PACKAGES=1 \
    POWERLEVEL9K_INSTANT_PROMPT=quiet

# Tool Versions
ARG AWS_CLI_VERSION=2.15.0
ARG KUBECTL_VERSION=v1.30.1
ARG PYTHON_VERSION=3.11.9
ARG TERRAFORM_VERSION=1.11.4
ARG TERRAGRUNT_VERSION=0.80.2
ARG VAULT_VERSION=1.17.0

# Create a non-root user
ARG USERNAME=devuser
ARG USER_UID=2000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME && \
    useradd --uid $USER_UID --gid $USER_GID -m $USERNAME && \
    usermod -aG sudo $USERNAME && \
    mkdir -p /etc/sudoers.d && \
    echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME

# Install base packages
RUN apt-get update && apt-get install -y \
    rsync curl unzip wget git jq gnupg ca-certificates \
    openssh-client software-properties-common \
    build-essential libssl-dev zlib1g-dev libbz2-dev \
    libreadline-dev libsqlite3-dev libncursesw5-dev xz-utils \
    tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev \
    zsh fzf fonts-powerline sudo \
    && rm -rf /var/lib/apt/lists/*

# Install Vault
RUN curl -fsSL https://releases.hashicorp.com/vault/${VAULT_VERSION}/vault_${VAULT_VERSION}_linux_amd64.zip -o vault.zip && \
    unzip vault.zip && \
    install -o root -g root -m 0755 vault /usr/local/bin/vault && \
    rm vault vault.zip

# Install AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${AWS_CLI_VERSION}.zip" -o awscliv2.zip && \
    unzip awscliv2.zip && ./aws/install && rm -rf aws awscliv2.zip

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && rm kubectl

# Install tfswitch and tgswitch
RUN curl -L https://raw.githubusercontent.com/warrensbox/terraform-switcher/release/install.sh | bash && \
    curl -L https://raw.githubusercontent.com/warrensbox/tgswitch/release/install.sh | bash

# Set ENV for pyenv
ENV PYENV_ROOT="/home/$USERNAME/.pyenv"
ENV PATH="${PYENV_ROOT}/bin:${PYENV_ROOT}/shims:/home/$USERNAME/.local/bin:/home/$USERNAME/bin:${PATH}"

# Install pyenv, Python, pipenv
USER $USERNAME
RUN curl -fsSL https://pyenv.run | bash && \
    ~/.pyenv/bin/pyenv install ${PYTHON_VERSION} && \
    ~/.pyenv/bin/pyenv global ${PYTHON_VERSION} && \
    pip install --upgrade pip pipenv

# Configure pyenv in shell
RUN echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc && \
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc && \
    echo 'eval "$(pyenv init --path)"' >> ~/.bashrc && \
    echo 'eval "$(pyenv init -)"' >> ~/.bashrc && \
    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc && \
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc && \
    echo 'eval "$(pyenv init --path)"' >> ~/.zshrc && \
    echo 'eval "$(pyenv init -)"' >> ~/.zshrc

# Install specific Terraform and Terragrunt versions
RUN tfswitch ${TERRAFORM_VERSION} && \
    tgswitch ${TERRAGRUNT_VERSION}

# Set working directory
WORKDIR /home/$USERNAME

# Set default user
USER $USERNAME

# Install Zsh plugins
RUN git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && \
    ~/.fzf/install --key-bindings --completion --no-update-rc && \
    mkdir -p ~/.zsh/plugins && \
    git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting ~/.zsh/plugins/zsh-syntax-highlighting && \
    git clone https://github.com/unixorn/fzf-zsh-plugin ~/.zsh/plugins/fzf-zsh-plugin && \
    echo '# Zsh plugin and FZF setup' >> ~/.zshrc && \
    echo 'source ~/.fzf/shell/key-bindings.zsh' >> ~/.zshrc && \
    echo 'source ~/.fzf/shell/completion.zsh' >> ~/.zshrc && \
    echo 'source ~/.zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh' >> ~/.zshrc && \
    echo 'source ~/.zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh' >> ~/.zshrc && \
    echo 'source ~/.zsh/plugins/fzf-zsh-plugin/fzf-zsh-plugin.plugin.zsh' >> ~/.zshrc

RUN echo '\n# Zsh history config' >> ~/.zshrc && \
    echo 'export HISTFILE=~/.zsh_history' >> ~/.zshrc && \
    echo 'export HISTSIZE=10000' >> ~/.zshrc && \
    echo 'export SAVEHIST=10000' >> ~/.zshrc && \
    echo 'setopt inc_append_history' >> ~/.zshrc && \
    echo 'setopt share_history' >> ~/.zshrc && \
    echo 'setopt hist_ignore_dups' >> ~/.zshrc && \
    echo 'setopt hist_find_no_dups' >> ~/.zshrc && \
    echo 'setopt hist_reduce_blanks' >> ~/.zshrc && \
    echo 'source ~/.zsh_aliases' >> ~/.zshrc
