FROM mcr.microsoft.com/devcontainers/dotnet:9.0

# Additional system packages (git, curl, jq, bash already included in base)
RUN apt-get update && apt-get install -y \
    locales \
    python3.11 \
    python3-pip \
    vim \
    tmux \
    xterm \
    unixodbc \
    gnupg2 \
    apt-transport-https

# Microsoft MSSQL ODBC driver + sqlcmd
RUN curl -fsSL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor -o /usr/share/keyrings/microsoft-prod.gpg && \
    curl -fsSL https://packages.microsoft.com/config/debian/12/prod.list > /etc/apt/sources.list.d/mssql-release.list && \
    apt-get update && \
    ACCEPT_EULA=Y apt-get install -y msodbcsql18 mssql-tools18 && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Node.js 22
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt-get install -y nodejs && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Global Node tools
RUN npm install -g @anthropic-ai/claude-code typescript tsx pnpm

# dotnet-ef
RUN dotnet tool install --global dotnet-ef
ENV PATH="${PATH}:/root/.dotnet/tools"

# Use the non-root vscode user provided by the devcontainers base image (UID 1000)
USER vscode
ENV PATH="${PATH}:/home/vscode/.local/bin:/home/vscode/.dotnet/tools:/usr/local/go/bin"

RUN curl https://mise.run | sh && \
    echo 'eval "$(~/.local/bin/mise activate bash)"' >> ~/.bashrc && \
    echo 'command -v resize &>/dev/null && eval "$(resize)"' >> ~/.bashrc

# dotnet-ef for vscode user
RUN dotnet tool install --global dotnet-ef

WORKDIR /home/vscode/workspace

# tmux config: true colour + ASCII-only borders for broad SSH client compatibility
RUN printf '%s\n' \
    'set -as terminal-features ",xterm-256color:RGB"' \
    'set -g default-terminal "tmux-256color"' \
    'set -g default-shell /bin/bash' \
    > /home/vscode/.tmux.conf

ENTRYPOINT ["tmux", "-u", "new-session", "-s", "main"]
