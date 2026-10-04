FROM mcr.microsoft.com/devcontainers/base:ubuntu

ARG USERNAME=vscode

# Werkzeuge für die Egress-Firewall:
# - iptables/ipset: Allowlist auf IP-Ebene
# - dnsutils (dig): Auflösung der erlaubten Domains zu IPs
RUN apt-get update && apt-get install -y --no-install-recommends \
        iptables \
        ipset \
        iproute2 \
        dnsutils \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Pre-create the .claude directory owned by the non-root user so that Docker
# seeds the named volume with correct ownership on first mount. Without this,
# Docker creates the mountpoint as root and the vscode user gets EACCES.
RUN mkdir -p /home/${USERNAME}/.claude && chown ${USERNAME}:${USERNAME} /home/${USERNAME}/.claude

USER ${USERNAME}
WORKDIR /workspaces/claude-workspace