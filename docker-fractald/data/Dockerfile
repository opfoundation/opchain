FROM debian:latest

# Install wget and other necessary tools
RUN apt-get update && apt-get install -y wget tar

# Set the working directory
WORKDIR /app

# Download and extract Fractal Bitcoin
RUN wget https://github.com/fractal-bitcoin/fractald-release/releases/download/v0.2.1/fractald-0.2.1-x86_64-linux-gnu.tar.gz && \
    tar -zxvf fractald-0.2.1-x86_64-linux-gnu.tar.gz && \
    rm fractald-0.2.1-x86_64-linux-gnu.tar.gz

# Set up the data directory
RUN mkdir -p /app/fractald-0.2.1-x86_64-linux-gnu/data && \
    cp /app/fractald-0.2.1-x86_64-linux-gnu/bitcoin.conf /app/fractald-0.2.1-x86_64-linux-gnu/data/

# Set the working directory to the extracted folder
WORKDIR /app/fractald-0.2.1-x86_64-linux-gnu

# Command to run the Bitcoin daemon
CMD ["./bin/bitcoind", "-datadir=./data/", "-maxtipage=504576000"]
