FROM mcr.microsoft.com/dotnet/sdk:9.0

# System packages
RUN apt-get update && apt-get install -y \
    curl \
    git \
    jq \
    python3.11 \
    python3-pip \
    unixodbc \
    gnupg2 \
    apt-transport-https \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

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

# dotnet-ef + nuke
RUN dotnet tool install --global dotnet-ef && \
    dotnet tool install --global Nuke.GlobalTool
ENV PATH="${PATH}:/root/.dotnet/tools"

# Create a non-root user
RUN useradd -m -s /bin/bash claude

# mise (version manager) — installed as the claude user
USER claude
ENV PATH="${PATH}:/home/claude/.local/bin:/home/claude/.dotnet/tools:/usr/local/go/bin"

RUN curl https://mise.run | sh && \
    echo 'eval "$(~/.local/bin/mise activate bash)"' >> ~/.bashrc

# dotnet-ef + nuke for the claude user
RUN dotnet tool install --global dotnet-ef && \
    dotnet tool install --global Nuke.GlobalTool

WORKDIR /home/claude/workspace

ENTRYPOINT ["claude"]
CMD ["--help"]
