FROM mcr.microsoft.com/devcontainers/base:noble
USER vscode
WORKDIR /home/vscode

# install pyenv and Python via pyenv
RUN sudo apt-get update && \
    sudo apt-get -y install \
        build-essential \
        curl \
        git \
        libssl-dev \
        zlib1g-dev \
        libbz2-dev \
        libreadline-dev \
        libsqlite3-dev \
        wget \
        llvm \
        libncurses5-dev \
        xz-utils \
        tk-dev \
        libxml2-dev \
        libxmlsec1-dev \
        libffi-dev \
        liblzma-dev && \
    sudo rm -rf /var/lib/apt/lists/*

# install pyenv
ENV PYENV_ROOT=/home/vscode/.pyenv
ENV PATH=$PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH
RUN git clone https://github.com/pyenv/pyenv.git $PYENV_ROOT

# install desired Python version via pyenv and set as global (latest stable)
RUN pyenv install 3.13.0 && pyenv global 3.13.0

# create python virtual environment using the pyenv-installed python
RUN python -m venv .venv
ENV PATH=/home/vscode/.venv/bin:$PATH

# install nodenv and Node.js via nodenv
ENV NODENV_ROOT=/home/vscode/.nodenv
ENV PATH=$NODENV_ROOT/shims:$NODENV_ROOT/bin:$PATH
RUN git clone https://github.com/nodenv/nodenv.git $NODENV_ROOT \
    && git clone https://github.com/nodenv/node-build.git $NODENV_ROOT/plugins/node-build

# install desired Node version via nodenv and set as global (latest LTS)
RUN nodenv install 24.13.0 && nodenv global 24.13.0

# install pypi packages
COPY requirements.txt .
RUN pip install -r requirements.txt && \
    pip uninstall -y python-zaqarclient && \
    rm requirements.txt

# install tools
RUN sudo apt-get update && \
    sudo apt-get -y install vim \
                            jq \
                            netcat-openbsd \
                            iputils-ping

# install Codex CLI via npm
RUN npm install -g @openai/codex
RUN nodenv rehash

# install tofuenv and opentofu
ENV TOFUENV=/home/vscode/.tofuenv
RUN git clone --depth 1 https://github.com/tofuutils/tofuenv $TOFUENV
ENV PATH=$TOFUENV/bin:$PATH
RUN tofuenv use 1.10.6
RUN sudo ln -s /home/vscode/.tofuenv/bin/tofu /usr/bin/terraform

## install packer
RUN sudo apt-get update && sudo apt-get install -y gnupg software-properties-common && \
    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
        sudo tee /etc/apt/sources.list.d/hashicorp.list && \
    sudo apt-get update && sudo apt-get install packer
