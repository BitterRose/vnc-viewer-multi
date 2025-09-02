ARG BUILD_FROM
FROM $BUILD_FROM AS no_vnc

RUN apk add --no-cache \
    git

RUN git config --global advice.detachedHead false && \
    git clone https://github.com/novnc/noVNC.git -b v1.5.0 /noVNC

FROM $BUILD_FROM AS websockify

RUN apk add --no-cache \
    git \
    python3 \
    py3-setuptools \
    py3-numpy \
    py3-pip \
    build-base \
    python3-dev

RUN pip3 install --break-system-packages --no-cache-dir Cython

# hadolint ignore=DL3003
RUN git config --global advice.detachedHead false && \
    git clone https://github.com/novnc/websockify -b v0.12.0 /websockify && \
    cd /websockify && python3 setup.py install

FROM $BUILD_FROM AS running

RUN apk add --no-cache \
    git \
    python3 \
    py3-setuptools \
    py3-numpy \
    procps

COPY --from=no_vnc /noVNC /noVNC

# Copy websockify installation
RUN PYTHON_VERSION=$(python3 -c "import sys; print(f'{sys.version_info.major}.{sys.version_info.minor}')") && \
    mkdir -p /usr/local/lib/python${PYTHON_VERSION}/site-packages/

COPY --from=websockify /usr/local/lib/python*/site-packages/ /tmp/websockify-packages/
COPY --from=websockify /usr/local/bin/websockify /usr/local/bin/websockify

RUN PYTHON_VERSION=$(python3 -c "import sys; print(f'{sys.version_info.major}.{sys.version_info.minor}')") && \
    cp -r /tmp/websockify-packages/* /usr/local/lib/python${PYTHON_VERSION}/site-packages/ && \
    rm -rf /tmp/websockify-packages

# Expose ports for multiple VNC proxies
EXPOSE 6080 6081 6082 6083 6084 6085 6086 6087 6088 6089

WORKDIR /
COPY root /

ENTRYPOINT [ "/init" ]
CMD []