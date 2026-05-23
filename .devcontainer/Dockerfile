FROM ghcr.io/linuxserver/baseimage-kasmvnc:ubuntujammy

# 1. Guarantee the DevContainer CLI executes this as root
USER root

# 2. Codespaces network fix: Force apt to use IPv4 (bypasses IPv6 timeouts)
#    and clear any corrupted package caches left over from the base image
RUN echo 'Acquire::ForceIPv4 "true";' > /etc/apt/apt.conf.d/99force-ipv4 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# STEP 1: Update and install core system utilities
# Added --no-install-recommends to keep the installation lean and avoid missing package loops
RUN apt-get update --fix-missing && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    curl \
    wget \
    gnupg \
    ca-certificates \
    apt-transport-https \
    git \
    sudo

# STEP 2: Add Google Chrome's repository securely
RUN curl -fsSL https://dl.google.com/linux/linux_signing_key.pub | gpg --dearmor -o /usr/share/keyrings/google-chrome.gpg && \
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/google-chrome.gpg] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list

# STEP 3: Install XFCE, Thunar, and Google Chrome
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    google-chrome-stable \
    xfce4 \
    xfce4-goodies \
    thunar \
    thunar-archive-plugin \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# STEP 4: Install Micro via official script to avoid missing Ubuntu repo errors
RUN curl https://getmic.ro | bash && \
    mv micro /usr/local/bin/

# STEP 5: Create a wrapper script for Chrome to enforce flags globally
RUN rm -f /usr/bin/google-chrome /usr/bin/google-chrome-stable && \
    echo '#!/bin/bash\nexec /opt/google/chrome/google-chrome --no-sandbox --disable-dev-shm-usage --ignore-gpu-blocklist --enable-webgl --use-gl=angle "$@"' > /usr/bin/google-chrome && \
    chmod +x /usr/bin/google-chrome && \
    ln -s /usr/bin/google-chrome /usr/bin/google-chrome-stable

# STEP 6: Configure KasmVNC to launch XFCE on startup
RUN mkdir -p /defaults && \
    echo "startxfce4" > /defaults/autostart
