FROM ${GRU_BASE_IMAGE}

ARG PYTHON_VERSION=3.14
ENV UV_PYTHON_INSTALL_DIR=/opt/uv-python \
    UV_PYTHON_PREFERENCE=only-managed
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends ca-certificates curl; \
    curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR=/usr/local/bin UV_NO_MODIFY_PATH=1 sh; \
    uv python install "${PYTHON_VERSION}" --default; \
    chmod -R a+rX /opt/uv-python; \
    rm -rf /var/lib/apt/lists/*; \
    uv --version; \
    uv run --python "${PYTHON_VERSION}" python --version
