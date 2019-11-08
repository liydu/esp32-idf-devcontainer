# https://github.com/espressif/esp-idf/tree/master/tools/docker

FROM alpine:latest

# This hack is widely applied to avoid python printing issues in docker containers.
# See: https://github.com/Docker-Hub-frolvlad/docker-alpine-python3/pull/13
ENV PYTHONUNBUFFERED=1

RUN echo "------ Install Dependencies ------" && \
    apk update && apk add --no-cache -X http://dl-cdn.alpinelinux.org/alpine/edge/testing \
    build-base \
    bison \
    ca-certificates \
    ccache \
    check \
    cmake \
    curl \
    flex \
    git \
    gperf \
    lcov \
    ncurses-dev \
    libusb-dev \
    libffi-dev \
    openssl-dev \
    ninja \
    python3 \
    python3-dev \
    unzip \
    wget \
    xz \
    zip

# https://github.com/Docker-Hub-frolvlad/docker-alpine-python3/blob/master/Dockerfile

RUN echo "------ Configure Python and Pip ------" && \
    if [ ! -e /usr/bin/python ]; then ln -sf python3 /usr/bin/python ; fi && \
    python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --no-cache --upgrade pip virtualenv setuptools wheel && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi

# To build the image for a branch or a tag of IDF, pass --build-arg IDF_CLONE_BRANCH_OR_TAG=name.
# To build the image with a specific commit ID of IDF, pass --build-arg IDF_CHECKOUT_REF=commit-id.
# It is possibe to combine both, e.g.:
#   IDF_CLONE_BRANCH_OR_TAG=release/vX.Y
#   IDF_CHECKOUT_REF=<some commit on release/vX.Y branch>.

# wget -O esp-idf.zip https://github.com/espressif/esp-idf/releases/download/v4.0-beta2/esp-idf-v4.0-beta2.zip

ARG IDF_CLONE_URL=https://github.com/espressif/esp-idf.git
ARG IDF_CLONE_BRANCH_OR_TAG=master
ARG IDF_CHECKOUT_REF=

ENV IDF_PATH=/opt/esp/idf
ENV IDF_TOOLS_PATH=/opt/esp

RUN echo IDF_CHECKOUT_REF=$IDF_CHECKOUT_REF IDF_CLONE_BRANCH_OR_TAG=$IDF_CLONE_BRANCH_OR_TAG && \
    git clone --recursive \
      ${IDF_CLONE_BRANCH_OR_TAG:+-b $IDF_CLONE_BRANCH_OR_TAG} \
      $IDF_CLONE_URL $IDF_PATH && \
    if [ -n "$IDF_CHECKOUT_REF" ]; then \
      cd $IDF_PATH && \
      git checkout $IDF_CHECKOUT_REF && \
      git submodule update --init --recursive; \
    fi

# Current there is problem for this: https://github.com/espressif/esp-idf/issues/4270
RUN $IDF_PATH/install.sh && \
  rm -rf $IDF_TOOLS_PATH/dist

COPY entrypoint.sh /opt/esp/entrypoint.sh

ENTRYPOINT [ "/opt/esp/entrypoint.sh" ]
CMD [ "/bin/bash" ]