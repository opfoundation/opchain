# Use an arm64 version of the base image
FROM arm64v8/debian:latest

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libtool \
    autotools-dev \
    automake \
    autoconf \
    pkg-config \
    libssl-dev \
    libevent-dev \
    bsdmainutils \
    python3 \
    libboost-system-dev \
    libboost-filesystem-dev \
    libboost-chrono-dev \
    libboost-test-dev \
    libboost-thread-dev \
    git \
    build-essential \
    autoconf \
    automake \
    libtool \
    libssl-dev \
    libevent-dev \
    pkg-config \
    bsdmainutils

# Clone the Bitcoin Core source code
RUN git clone https://github.com/bitcoin/bitcoin.git

# Build Bitcoin Core from source
WORKDIR /bitcoin
RUN ls -la  # List files to ensure autogen.sh exists
RUN ./autogen.sh || true  # Skip if not found (in case it's unnecessary)
RUN ./configure
RUN make
RUN make install