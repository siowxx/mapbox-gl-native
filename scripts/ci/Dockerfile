FROM ubuntu:disco-20190718

ENV DEBIAN_FRONTEND="noninteractive"
ENV ANDROID_HOME="/opt/android"

WORKDIR /src

# Use faster mirrors from Finland
COPY etc/apt/sources.list /etc/apt

RUN set -eu \
    && apt-get update \
    && apt-get dist-upgrade -y \
    && apt-get autoremove --purge -y && apt-get clean

# CI requirements
RUN set -eu && apt-get install -y \
      ca-certificates \
      git \
      gzip \
      ssh \
      tar \
    && apt-get autoremove --purge -y && apt-get clean

# Base dependencies
RUN set -eu && apt-get install -y \
      ccache \
      clang-8 \
      clang-format-8 \
      clang-tidy-8 \
      cmake \
      fonts-noto \
      g++-8 \
      libc++-8-dev \
      libc++abi-8-dev \
      mesa-common-dev \
      ninja-build \
      nodejs \
      npm \
      pkg-config \
      python3-pip \
      python3-requests \
      python3-github \
      software-properties-common \
      valgrind \
      xvfb \
    && apt-get autoremove --purge -y && apt-get clean

RUN pip3 install cmake-format==0.5.5

# Linux dependencies
RUN set -eu && apt-get install -y \
      libcurl4-openssl-dev \
      libgl1-mesa-dev \
      libgles2-mesa-dev \
      libglfw3-dev \
      libicu-dev \
      libjpeg-turbo8-dev \
      libpng-dev \
      libuv1-dev \
      zlib1g-dev \
    && apt-get autoremove --purge -y && apt-get clean

# Qt dependencies
RUN set -eu && apt-get install -y \
      qdoc-qt5 \
      qt5-default \
    && apt-get autoremove --purge -y && apt-get clean

# Android dependencies
RUN set -eu && apt-get install -y \
      coreutils \
      curl \
      openjdk-8-jdk-headless \
      unzip \
    && apt-get autoremove --purge -y && apt-get clean

# Install old compilers
RUN set -eu \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1E9377A2BA9EF27F \
    && add-apt-repository "deb http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu xenial main" \
    && add-apt-repository "deb http://us.archive.ubuntu.com/ubuntu/ xenial main universe" \
    && apt-get install -y g++-5 \
    && apt-get autoremove --purge -y && apt-get clean

# Install Android NDK
RUN set -eu \
    && mkdir -p ${ANDROID_HOME} && cd ${ANDROID_HOME} \
    && curl -L --retry 3 https://dl.google.com/android/repository/android-ndk-r19-linux-x86_64.zip -o ndk.zip \
    && (echo "f02ad84cb5b6e1ff3eea9e6168037c823408c8ac  ndk.zip" | sha1sum -c) \
    && unzip -q ndk.zip && rm ndk.zip && mv android-ndk-r* ndk-bundle


RUN set -eu \
    && cd ${ANDROID_HOME} \
    && curl -L --retry 3 https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip -o tools.zip \
    && (echo "92ffee5a1d98d856634e8b71132e8a95d96c83a63fde1099be3d86df3106def9  tools.zip" | sha256sum -c) \
    && unzip -q tools.zip && rm tools.zip

RUN set -eu \
    && yes | ${ANDROID_HOME}/tools/bin/sdkmanager \
        "platform-tools" \
        "platforms;android-26" \
        "build-tools;26.0.3" \
        "platforms;android-27" \
        "build-tools;27.0.3" \
        "platforms;android-28" \
        "build-tools;28.0.3" \
        "extras;android;m2repository" \
        "patcher;v4" \
        "extras;google;m2repository" \
        "extras;m2repository;com;android;support;constraint;constraint-layout;1.0.2" \
        "cmake;3.10.2.4988404"

# Install gcloud for Firebase testing
RUN set -eu \
    && echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list \
    && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add - \
    && apt-get update -y \
    && apt-get install google-cloud-sdk -y \
    && apt-get autoremove --purge -y && apt-get clean

# Configure ccache
RUN set -eu && /usr/sbin/update-ccache-symlinks
