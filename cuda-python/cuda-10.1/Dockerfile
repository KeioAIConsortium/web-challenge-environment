# # LICENSE
# ## Python
#
# このファイルには https://github.com/docker-library/python/blob/master/3.7/stretch/Dockerfile をもとに
# 改変したコードが含まれています
#
# 主な改変内容:
# - Ubuntu でのビルド時に必要なパッケージを変更
# - ロケールの変更
# - Python ビルドの簡略化
# - pip ライブラリのインストールに関するコードを追記
#
# Copyright © 2001-2020 Python Software Foundation; All Rights Reserved.
#
# 1. This LICENSE AGREEMENT is between the Python Software Foundation ("PSF"), and
#    the Individual or Organization ("Licensee") accessing and otherwise using Python
#    3.7.9 software in source or binary form and its associated documentation.

# 2. Subject to the terms and conditions of this License Agreement, PSF hereby
#    grants Licensee a nonexclusive, royalty-free, world-wide license to reproduce,
#    analyze, test, perform and/or display publicly, prepare derivative works,
#    distribute, and otherwise use Python 3.7.9 alone or in any derivative
#    version, provided, however, that PSF's License Agreement and PSF's notice of
#    copyright, i.e., "Copyright © 2001-2020 Python Software Foundation; All Rights
#    Reserved" are retained in Python 3.7.9 alone or in any derivative version
#    prepared by Licensee.

# 3. In the event Licensee prepares a derivative work that is based on or
#    incorporates Python 3.7.9 or any part thereof, and wants to make the
#    derivative work available to others as provided herein, then Licensee hereby
#    agrees to include in any such work a brief summary of the changes made to Python
#    3.7.9.

# 4. PSF is making Python 3.7.9 available to Licensee on an "AS IS" basis.
#    PSF MAKES NO REPRESENTATIONS OR WARRANTIES, EXPRESS OR IMPLIED.  BY WAY OF
#    EXAMPLE, BUT NOT LIMITATION, PSF MAKES NO AND DISCLAIMS ANY REPRESENTATION OR
#    WARRANTY OF MERCHANTABILITY OR FITNESS FOR ANY PARTICULAR PURPOSE OR THAT THE
#    USE OF PYTHON 3.7.9 WILL NOT INFRINGE ANY THIRD PARTY RIGHTS.

# 5. PSF SHALL NOT BE LIABLE TO LICENSEE OR ANY OTHER USERS OF PYTHON 3.7.9
#    FOR ANY INCIDENTAL, SPECIAL, OR CONSEQUENTIAL DAMAGES OR LOSS AS A RESULT OF
#    MODIFYING, DISTRIBUTING, OR OTHERWISE USING PYTHON 3.7.9, OR ANY DERIVATIVE
#    THEREOF, EVEN IF ADVISED OF THE POSSIBILITY THEREOF.

# 6. This License Agreement will automatically terminate upon a material breach of
#    its terms and conditions.

# 7. Nothing in this License Agreement shall be deemed to create any relationship
#    of agency, partnership, or joint venture between PSF and Licensee.  This License
#    Agreement does not grant permission to use PSF trademarks or trade name in a
#    trademark sense to endorse or promote products or services of Licensee, or any
#    third party.

# 8. By copying, installing or otherwise using Python 3.7.9, Licensee agrees
#    to be bound by the terms and conditions of this License Agreement.

FROM keioaic/web-challenge-environment:cuda10.1

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
