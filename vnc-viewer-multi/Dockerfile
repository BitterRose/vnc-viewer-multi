ARG BUILD_FROM
FROM $BUILD_FROM

# Install all required packages in one layer
RUN apk add --no-cache \
    git \
    python3 \
    py3-setuptools \
    py3-numpy \
    py3-pip \
    build-base \
    python3-dev \
    procps \
    bash

# Clone your custom noVNC fork
RUN git config --global advice.detachedHead false && \
    git clone https://github.com/BitterRose/noVNC-BitterRose.git /noVNC

# Install websockify
RUN pip3 install --break-system-packages --no-cache-dir websockify

# Make sure websockify is executable
RUN chmod +x /usr/bin/websockify || chmod +x /usr/local/bin/websockify || true

# Find and create symlink if websockify is not in expected location
RUN if [ ! -f /usr/local/bin/websockify ]; then \
        WEBSOCKIFY_PATH=$(find /usr -name websockify -type f -executable 2>/dev/null | head -1); \
        if [ -n "$WEBSOCKIFY_PATH" ]; then \
            ln -sf "$WEBSOCKIFY_PATH" /usr/local/bin/websockify; \
        fi; \
    fi

# Verify websockify installation
RUN websockify --help > /dev/null || { echo "Websockify installation failed"; exit 1; }

# Expose ports for multiple VNC proxies
EXPOSE 6080 6081 6082 6083 6084 6085 6086 6087 6088 6089

WORKDIR /
COPY root /

ENTRYPOINT [ "/init" ]
CMD []