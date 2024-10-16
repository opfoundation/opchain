# Running Opimize on Debian

This guide explains how to run Fractal Bitcoin on Debian, both directly on the system and using Docker.

## Table of Contents
1. [Running Directly on Debian](#running-directly-on-debian)
2. [Running with Docker](#running-with-docker)
3. [Troubleshooting](#troubleshooting)

## Running Directly on Debian

1. Update your system:
   ```
   sudo apt update && sudo apt upgrade -y
   ```

2. Install required dependencies:
   ```
   sudo apt install wget tar -y
   ```

3. Download the Fractal Bitcoin release:
   ```
   wget https://github.com/fractal-bitcoin/fractald-release/releases/download/v0.2.1/fractald-0.2.1-x86_64-linux-gnu.tar.gz
   ```

4. Extract the files:
   ```
   tar -zxvf fractald-0.2.1-x86_64-linux-gnu.tar.gz
   ```

5. Navigate to the directory:
   ```
   cd fractald-0.2.1-x86_64-linux-gnu
   ```

6. Set up the data directory:
   ```
   mkdir data
   cp ./bitcoin.conf ./data
   ```

7. Run the Bitcoin daemon:
   ```
   ./bin/bitcoind -datadir=./data/ -maxtipage=504576000
   ```

## Running with Docker

1. Install Docker and Docker Compose:
   ```
   sudo apt update
   sudo apt install docker.io docker-compose -y
   ```

2. Start and enable Docker:
   ```
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

3. Add your user to the docker group (log out and back in after this):
   ```
   sudo usermod -aG docker $USER
   ```

4. Create a new directory for your Fractal Bitcoin project:
   ```
   mkdir fractal-bitcoin && cd fractal-bitcoin
   ```

5. Create a file named `Dockerfile` with the following content:
   ```dockerfile
   FROM debian:latest

   RUN apt-get update && apt-get install -y wget tar

   WORKDIR /app

   RUN wget https://github.com/fractal-bitcoin/fractald-release/releases/download/v0.2.1/fractald-0.2.1-x86_64-linux-gnu.tar.gz && \
       tar -zxvf fractald-0.2.1-x86_64-linux-gnu.tar.gz && \
       rm fractald-0.2.1-x86_64-linux-gnu.tar.gz

   RUN mkdir -p /app/fractald-0.2.1-x86_64-linux-gnu/data && \
       cp /app/fractald-0.2.1-x86_64-linux-gnu/bitcoin.conf /app/fractald-0.2.1-x86_64-linux-gnu/data/

   WORKDIR /app/fractald-0.2.1-x86_64-linux-gnu

   CMD ["./bin/bitcoind", "-datadir=./data/", "-maxtipage=504576000"]
   ```

6. Create a file named `docker-compose.yml` with the following content:
   ```yaml
   version: '3'
   services:
     fractald:
       build: .
       volumes:
         - ./data:/app/fractald-0.2.1-x86_64-linux-gnu/data
       ports:
         - "8332:8332"
         - "8333:8333"
       restart: unless-stopped
   ```

7. Create a data directory:
   ```
   mkdir data
   ```

8. Build and run the container:
   ```
   docker-compose up -d
   ```

9. To stop the container:
   ```
   docker-compose down
   ```

10. To view logs:
    ```
    docker-compose logs -f
    ```

## Troubleshooting

- If you encounter permission issues with Docker, make sure you've added your user to the docker group and logged out and back in.
- If the Bitcoin daemon fails to start, check the logs for any error messages:
  ```
  docker-compose logs fractald
  ```
- Ensure you have enough disk space for the blockchain data. You can check your disk space with:
  ```
  df -h
  ```
- If you're having network issues, check your firewall settings and ensure ports 8332 and 8333 are open. You can use the following command to check if the ports are listening:
  ```
  sudo netstat -tuln | grep -E '8332|8333'
  ```
- If you need to customize the Bitcoin configuration, you can edit the `bitcoin.conf` file in the `data` directory before starting the container.

## Additional Notes

- The Docker setup uses Debian as the base image, ensuring compatibility with your host system.
- The blockchain data is stored in the `./data` directory on your host machine, allowing for easy backups and persistence across container restarts.
- You can interact with the Bitcoin daemon using the `bitcoin-cli` tool. If running directly on the system:
  ```
  ./bin/bitcoin-cli -datadir=./data/ getblockcount
  ```
  If using Docker:
  ```
  docker-compose exec fractald ./bin/bitcoin-cli -datadir=./data/ getblockcount
  ```

For more detailed information, refer to the [Fractal Bitcoin documentation](https://github.com/fractal-bitcoin/fractald-release).
