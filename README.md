<h1 align="center">File Shuttle</h1>

<h5 align="center">Streamlined File Transfers with gRPC and HTTP/2</h5>

<br/>

### Introduction

File Shuttle is a versatile tool designed to explore the efficiency of file uploads using both gRPC and HTTP/2 protocols. This project provides a comparative analysis of the two approaches.

Explore the code and experiment with various scenarios to understand how these protocols handle large file transfers.


```
NAME:
   fileshuttle - Efficient file transfer utility

USAGE:
   fileshuttle [global options] command [command options] [arguments...]

VERSION:
   1.0.0

COMMANDS:
     server    starts a file transfer server
     send      uploads a file to the server
     help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --verbose    enable detailed logging (default: false)
   --help, -h     show help (default: false)
   --version, -v  print the version (default: false)
```

Utilize the `server` command to initiate a file transfer server, and the `send` command to upload files to the specified server address.


#### HTTP/2 Configuration

To leverage HTTP/2, you'll need to configure TLS/SSL certificates. This ensures a secure connection between the client and server.

The server requires both the certificate and private key. In contrast, the client only needs the server's certificate.

Sample certificates are provided in the `certs` directory for your convenience. You can regenerate them at any time using `make certs`.

```sh
# Initiate the HTTP/2 server, leveraging TLS with provided certificate and key.
fileshuttle server \
        --port 8080 \
        --http2 \
        --cert ./certs/server.crt \
        --key ./certs/server.key


# Send the file `data.txt` to the server at `localhost:8080` using HTTP/2. 
# The client trusts the server certificate provided.
fileshuttle send \
        --http2 \
        --address localhost:8080 \
        --cert ./certs/server.crt \
        --file data.txt 
```

**Important:** The certificates are generated with a specific hostname. Ensure that the hostname you connect to matches the hostname in the certificate's Subject Alternative Name (SAN) or Common Name (CN) field. Otherwise, the client may reject the connection due to certificate mismatch.


#### gRPC Configuration

gRPC is the default protocol used for file transfers. If you don't specify `--http2`, File Shuttle will automatically use gRPC.

gRPC offers two primary modes:

- **Plain-text TCP**: No certificates are required.
- **TLS-based HTTP/2**: Requires certificates and keys.

**Plain-text TCP example:**

```sh
# Start the gRPC server using plain-text TCP.
fileshuttle server

# Send a file using plain-text TCP.
fileshuttle send --file myfile.zip 
```

**TLS-based HTTP/2 example:**

```sh
# Start the gRPC server with TLS enabled.
fileshuttle server \
        --cert ./certs/server.crt \
        --key ./certs/server.key

# Send a file using TLS-based HTTP/2.
fileshuttle send \
        --cert ./certs/server.crt \
        --file mydata.bin
```