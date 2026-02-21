ARG BASE=python:3.14-bookworm

FROM $BASE
ARG BASE

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    rm -f /etc/apt/apt.conf.d/docker-clean; \
    echo 'Binary::apt::APT::Keep-Downloaded-Packages "true";' > /etc/apt/apt.conf.d/keep-cache \
    && apt-get update && apt-get install -yq --no-install-recommends \
        git \
        less \
        locales \
        stow \
        tmux \
        vim \
        zsh \
    && locale-gen ru_RU.UTF-8 \
    && useradd -m user -s /usr/bin/zsh \
    && case "$BASE" in \
           node:*) npm i -g js-yaml && ln -s /usr/local/lib/node_modules /node_modules ;; \
       esac

ENV LANG=ru_RU.UTF-8 \
    LC_ALL=ru_RU.UTF-8 \
    TERM=xterm-256color \
    SHELL=/usr/bin/zsh \
    HOME=/home/user

WORKDIR /home/user
VOLUME ["/home/user"]

USER user

RUN git clone https://github.com/kyzima-spb/profile-dotfiles.git .dotfiles \
    && stow -d .dotfiles . \
    && git clone https://github.com/tmux-plugins/tpm .config/tmux/plugins/tpm \
    && tmux start-server \
    && tmux new-session -d -s build \
    && tmux source-file .config/tmux/tmux.conf \
    && .config/tmux/plugins/tpm/bin/install_plugins \
    && tmux kill-session -t build \
    && wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O- | sh -s -- --unattended --keep-zshrc

CMD ["tmux"]

