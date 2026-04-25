# wolfSSL TLS 1.3 — VS Code (Linux) Setup

This repository contains a minimal TLS 1.3 server and client using wolfSSL (ECDHE key exchange). The original instructions were targeted at Visual Studio; this README shows how to install wolfSSL and set up the project for development using Visual Studio Code on Linux.

## Setup

```bash
sudo apt update
sudo apt install -y build-essential git cmake pkg-config openssl
```

## Install wolfSSL

Build and install from source (for TLS 1.3 and ML-KEM):

```bash
git clone https://github.com/wolfSSL/wolfssl.git
cd wolfssl
mkdir build && cd build
cmake -DWOLFSSL_TLS13=ON -DCMAKE_BUILD_TYPE=Release ..
make -j$(nproc)
sudo make install
sudo ldconfig
```

## Generate Test Certificates

The repository includes a `certs/` folder. To generate new test certs (ECDSA P-256):

```bash
cd $(git rev-parse --show-toplevel)
mkdir -p certs && cd certs
openssl ecparam -genkey -name prime256v1 -out ca-key.pem
openssl req -new -x509 -key ca-key.pem -out ca-cert.pem -days 365 -subj "/CN=Test CA"
openssl ecparam -genkey -name prime256v1 -out server-key.pem
openssl req -new -key server-key.pem -out server.csr -subj "/CN=localhost"
openssl x509 -req -in server.csr -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem -days 365
```

## Build & Run

```bash
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DWOLFSSL_ROOT=/usr/local/include/wolfssl
cmake --build . --config Release

# Run server (background)
./server &

# Run client
./client
```