FROM ${GRU_BASE_IMAGE}

# Leave UV_VERSION empty to install the latest release at build time.
ARG UV_VERSION=
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends ca-certificates curl python3 python3-venv; \
    if [ -z "${UV_VERSION}" ]; then \
      curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR=/usr/local/bin UV_NO_MODIFY_PATH=1 sh; \
    else \
      curl -LsSf "https://astral.sh/uv/${UV_VERSION}/install.sh" | env UV_INSTALL_DIR=/usr/local/bin UV_NO_MODIFY_PATH=1 sh; \
    fi; \
    rm -rf /var/lib/apt/lists/*; \
    uv --version; \
    python3 --version
