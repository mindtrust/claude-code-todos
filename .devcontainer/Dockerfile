FROM node:20

ARG TZ=Asia/Tokyo
ENV TZ=${TZ}

# 日本語ロケールのインストールと設定（より確実な方法）
RUN apt-get update && apt-get install -y \
    locales \
    locales-all \
    tzdata \
    && rm -rf /var/lib/apt/lists/*

# 既存のロケール設定をクリーンアップ
RUN rm -f /etc/locale.gen

# 日本語ロケールを確実に設定
RUN echo "ja_JP.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen && \
    update-locale LANG=ja_JP.UTF-8

# /etc/default/localeファイルを直接編集
RUN echo "LANG=ja_JP.UTF-8" > /etc/default/locale && \
    echo "LANGUAGE=ja_JP:ja" >> /etc/default/locale && \
    echo "LC_ALL=ja_JP.UTF-8" >> /etc/default/locale

# 環境変数の設定
ENV LANG=ja_JP.UTF-8
ENV LC_ALL=ja_JP.UTF-8
ENV LANGUAGE=ja_JP:ja

# タイムゾーンの設定
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime && echo ${TZ} > /etc/timezone

# ロケール設定の確認（デバッグ用）
RUN locale-gen --purge ja_JP.UTF-8 && \
    dpkg-reconfigure --frontend=noninteractive locales

# Install basic development tools and iptables/ipset
RUN apt-get update && apt-get install -y less \
  git \
  procps \
  sudo \
  fzf \
  zsh \
  man-db \
  unzip \
  gnupg2 \
  gh \
  iptables \
  ipset \
  iproute2 \
  dnsutils \
  aggregate \
  jq \
  && rm -rf /var/lib/apt/lists/*

# Install git-secrets
RUN git clone https://github.com/awslabs/git-secrets.git /tmp/git-secrets && \
    cd /tmp/git-secrets && \
    make install && \
    rm -rf /tmp/git-secrets

# Ensure default node user has access to /usr/local/share
RUN mkdir -p /usr/local/share/npm-global && \
  chown -R node:node /usr/local/share

ARG USERNAME=node

# Persist bash history.
RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
  && mkdir /commandhistory \
  && touch /commandhistory/.bash_history \
  && chown -R $USERNAME /commandhistory

# Set `DEVCONTAINER` environment variable to help with orientation
ENV DEVCONTAINER=true

# Create workspace and config directories and set permissions
RUN mkdir -p /workspace /home/node/.claude && \
  chown -R node:node /workspace /home/node/.claude

# nodeユーザーの環境設定ファイルにロケール設定を追加
RUN echo 'export LANG=ja_JP.UTF-8' >> /home/node/.bashrc && \
    echo 'export LC_ALL=ja_JP.UTF-8' >> /home/node/.bashrc && \
    echo 'export LANGUAGE=ja_JP:ja' >> /home/node/.bashrc && \
    echo 'export TZ=Asia/Tokyo' >> /home/node/.bashrc && \
    chown node:node /home/node/.bashrc

# zshrcファイルにも設定を追加
RUN echo 'export LANG=ja_JP.UTF-8' >> /home/node/.zshrc && \
    echo 'export LC_ALL=ja_JP.UTF-8' >> /home/node/.zshrc && \
    echo 'export LANGUAGE=ja_JP:ja' >> /home/node/.zshrc && \
    echo 'export TZ=Asia/Tokyo' >> /home/node/.zshrc && \
    chown node:node /home/node/.zshrc

WORKDIR /workspace

RUN ARCH=$(dpkg --print-architecture) && \
  wget "https://github.com/dandavison/delta/releases/download/0.18.2/git-delta_0.18.2_${ARCH}.deb" && \
  dpkg -i "git-delta_0.18.2_${ARCH}.deb" && \
  rm "git-delta_0.18.2_${ARCH}.deb"

# Set up non-root user
USER node

# Install global packages
ENV NPM_CONFIG_PREFIX=/usr/local/share/npm-global
ENV PATH=$PATH:/usr/local/share/npm-global/bin

# Set the default shell to zsh rather than sh
ENV SHELL=/bin/zsh

# Default powerline10k theme
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.2.0/zsh-in-docker.sh)" -- \
  -p git \
  -p fzf \
  -a "source /usr/share/doc/fzf/examples/key-bindings.zsh" \
  -a "source /usr/share/doc/fzf/examples/completion.zsh" \
  -a "export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
  -a "export LANG=ja_JP.UTF-8" \
  -a "export LC_ALL=ja_JP.UTF-8" \
  -a "export LANGUAGE=ja_JP:ja" \
  -a "export TZ=Asia/Tokyo" \
  -x

# Install Claude
RUN npm install -g @anthropic-ai/claude-code

USER node
