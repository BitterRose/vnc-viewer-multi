ARG BUILD_FROM
FROM $BUILD_FROM AS no_vnc

RUN apt-get update && apt-get -y --no-install-recommends install \
    git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN git config --global advice.detachedHead false && \
    git clone https://github.com/novnc/noVNC.git -b v1.5.0 /noVNC

FROM $BUILD_FROM AS websockify

RUN apt-get update && apt-get -y --no-install-recommends install \
    git \
    python3 \
    python3-setuptools \
    python3-numpy \
    python3-pip \
    build-essential \
    python3-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN pip3 install --break-system-packages --no-cache-dir Cython

# hadolint ignore=DL3003
RUN git config --global advice.detachedHead false && \
    git clone https://github.com/novnc/websockify -b v0.12.0 /websockify && \
    cd /websockify && python3 setup.py install

FROM $BUILD_FROM AS running

RUN apt-get update && apt-get -y --no-install-recommends install \
    git \
    python3 \
    python3-setuptools \
    python3-numpy \
    procps \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY --from=no_vnc /noVNC /noVNC
COPY --from=websockify /usr/local/lib/python3.11/dist-packages/ /usr/local/lib/python3.11/dist-packages/
COPY --from=websockify /usr/local/bin/websockify /usr/local/bin/websockify

# Expose ports for multiple VNC proxies
EXPOSE 6080 6081 6082 6083 6084 6085 6086 6087 6088 6089

ENTRYPOINT [ "/init" ]
CMD []
COPY root /