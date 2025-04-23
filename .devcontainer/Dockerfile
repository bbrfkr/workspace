FROM mcr.microsoft.com/devcontainers/base:noble

RUN apt update && \
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

RUN git clone --depth 1 https://github.com/pyenv/pyenv /pyenv
RUN git clone --depth 1 https://github.com/rbenv/rbenv /rbenv && \
    git clone --depth 1 https://github.com/rbenv/ruby-build.git /rbenv/plugins/ruby-build
RUN git clone --depth 1 https://github.com/nodenv/nodenv /nodenv && \
    git clone --depth 1 https://github.com/nodenv/node-build.git /nodenv/plugins/node-build

ENV PATH=/pyenv/bin:/rbenv/bin:/nodenv/bin:$PATH
ENV PYENV_ROOT=/pyenv
ENV RBENV_ROOT=/rbenv
ENV NODENV_ROOT=/nodenv

RUN pyenv install 3.11
RUN rbenv install 3.3.8
RUN nodenv install 20.19.0

ENV PATH=/pyenv/shims:/rbenv/shims:/nodenv/shims:$PATH

RUN pyenv global 3.11
RUN rbenv global 3.3.8
RUN nodenv global 20.19.0