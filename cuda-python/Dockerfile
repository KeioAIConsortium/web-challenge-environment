FROM kumassy/foo-bar-images:cuda10.0

ENV DEBIAN_FRONTEND=noninteractive

ENV TZ Asia/Tokyo
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:jp
ENV LC_CTYPE C.UTF-8
ENV PIP_NO_CACHE_DIR "true"

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends \
    build-essential \
    libbz2-dev \
    libdb-dev \
    libreadline-dev \
    libffi-dev \
    libgdbm-dev \
    liblzma-dev \
    zlib1g-dev \
    uuid-dev \
    tk-dev \
    libncursesw5-dev \
    libsqlite3-dev \
    libssl-dev \
    wget \
    curl \
    git \
    cmake \
    libblas3 \
    libblas-dev \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*

ENV PYTHON_VERSION 3.7.5

RUN set -ex \
    \
    && wget -O python.tar.tgz https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz \
    && mkdir -p /usr/src/python \
    && tar -xzC /usr/src/python --strip-components=1 -f python.tar.tgz \
    && rm python.tar.tgz \
    && cd /usr/src/python \
    && ./configure \
        --enable-shared  \
        --without-ensurepip \
    && make -j "$(nproc)" \
    && make install \
    && ldconfig \
    \
    && find /usr/local -depth \
        \( \
            \( -type d -a \( -name test -o -name tests -o -name idle_test \) \) \
            -o \
            \( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
        \) -exec rm -rf '{}' + \
    && rm -rf /usr/src/python \
    \
    && python3 --version

ENV PYTHON_PIP_VERSION 20.1.1
# https://github.com/pypa/get-pip
ENV PYTHON_GET_PIP_URL https://github.com/pypa/get-pip/raw/eff16c878c7fd6b688b9b4c4267695cf1a0bf01b/get-pip.py
ENV PYTHON_GET_PIP_SHA256 b3153ec0cf7b7bbf9556932aa37e4981c35dc2a2c501d70d91d2795aa532be79

RUN set -ex; \
    \
    wget -O get-pip.py "$PYTHON_GET_PIP_URL"; \
    echo "$PYTHON_GET_PIP_SHA256 *get-pip.py" | sha256sum --check --strict -; \
    \
    python3 get-pip.py \
        --disable-pip-version-check \
        --no-cache-dir \
        "pip==$PYTHON_PIP_VERSION" \
    ; \
    pip3 --version; \
    \
    find /usr/local -depth \
        \( \
            \( -type d -a \( -name test -o -name tests -o -name idle_test \) \) \
            -o \
            \( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
        \) -exec rm -rf '{}' +; \
    rm -f get-pip.py

# install python dependencies system wide via poetry
ADD poetry.lock .
ADD pyproject.toml .
RUN set -ex \
    && pip3 install poetry==1.0.5 \
    && poetry config virtualenvs.create false \
    && poetry install
