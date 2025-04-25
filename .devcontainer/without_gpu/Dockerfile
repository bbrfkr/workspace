FROM mcr.microsoft.com/devcontainers/base:noble

# root user configuration
## install language dependencies
RUN apt-get update && \
    # for python
    apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev \
                       libsqlite3-dev curl git libncursesw5-dev xz-utils \
                       tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev && \
    # for ruby
    apt-get install -y autoconf patch build-essential rustc libssl-dev \
                       libyaml-dev libreadline6-dev zlib1g-dev libgmp-dev libncurses5-dev \
                       libffi-dev libgdbm6 libgdbm-dev libdb-dev uuid-dev && \
    # for nodejs
    apt-get install -y python3 g++-12 gcc-12 make python3-pip

# install docker
RUN apt-get update && \
    apt-get install -y ca-certificates curl && \
    install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && \
    chmod a+r /etc/apt/keyrings/docker.asc && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" \
        > /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# vscode user configuration
## create the user
ARG USER
RUN useradd --home-dir /home/$USER --create-home $USER\
    && echo $USER ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USER \
    && chmod 0440 /etc/sudoers.d/$USER \
    && cp /home/vscode/.bashrc /home/$USER \
    && usermod -aG docker $USER

## install language
RUN git clone --depth 1 https://github.com/pyenv/pyenv /var/lib/pyenv
RUN git clone --depth 1 https://github.com/rbenv/rbenv /var/lib/rbenv && \
    git clone --depth 1 https://github.com/rbenv/ruby-build.git /var/lib/rbenv/plugins/ruby-build
RUN git clone --depth 1 https://github.com/nodenv/nodenv /var/lib/nodenv && \
    git clone --depth 1 https://github.com/nodenv/node-build.git /var/lib/nodenv/plugins/node-build

RUN chown $USER:$USER -R /var/lib/pyenv /var/lib/rbenv /var/lib/nodenv

ENV PATH=/var/lib/pyenv/bin:/var/lib/rbenv/bin:/var/lib/nodenv/bin:$PATH
ENV PYENV_ROOT=/var/lib/pyenv
ENV RBENV_ROOT=/var/lib/rbenv
ENV NODENV_ROOT=/var/lib/nodenv

RUN pyenv install 3.11
RUN rbenv install 3.3.8
RUN nodenv install 20.19.0

ENV PATH=/var/lib/pyenv/shims:/var/lib/rbenv/shims:/var/lib/nodenv/shims:$PATH

RUN pyenv global 3.11 && rbenv global 3.3.8 && nodenv global 20.19.0

## install pip packages
COPY requirements.txt .
RUN pip install -r requirements.txt && rm -f requirements.txt && \
    pip uninstall -y python-zaqarclient

## install gem packages
COPY Gemfile .
RUN gem install bundle && bundle install

ENTRYPOINT [ "dockerd" ]
