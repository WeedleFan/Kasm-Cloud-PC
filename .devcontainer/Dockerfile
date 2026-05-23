FROM ghcr.io/linuxserver/baseimage-kasmvnc:ubuntujammy

# Install Git, Sudo, XFCE, Micro, Thunar, Thunar Archive Plugin
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    curl \
    wget \
    gnupg \
    git \
    sudo \
    micro \
    xfce4 \
    xfce4-goodies \
    thunar \
    thunar-archive-plugin \
    && \
    # Add Google's official repository and install Chrome
    wget -qO- https://dl.google.com/linux/linux_signing_key.pub | gpg --dearmor > /usr/share/keyrings/google-chrome.gpg && \
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/google-chrome.gpg] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list && \
    apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y google-chrome-stable && \
    # Clean up to keep the image size down
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Safely create a wrapper script for Chrome to enforce flags globally
# This removes the default symlinks and points our wrapper directly to the binary
RUN rm -f /usr/bin/google-chrome /usr/bin/google-chrome-stable && \
    echo '#!/bin/bash\nexec /opt/google/chrome/google-chrome --no-sandbox --disable-dev-shm-usage --ignore-gpu-blocklist --enable-webgl --use-gl=angle "$@"' > /usr/bin/google-chrome && \
    chmod +x /usr/bin/google-chrome && \
    ln -s /usr/bin/google-chrome /usr/bin/google-chrome-stable

# Configure KasmVNC to launch XFCE on startup
RUN mkdir -p /defaults && \
    echo "startxfce4" > /defaults/autostart
