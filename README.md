# gRPC Hands-on training
```
This git provide some protobuf+gRPC example codes to training.

Please follow Chapters & Steps.
```

## Chapter 0. Pre-requisites

### Step 1. Supplies
```
- java 8, gradle, python3, pip3, docker...

- Protocol Compiler
  > brew install protobuf

- Python3 gRPC modules
  > pip3 install --upgrade pip
  > pip3 install grpcio
  > pip3 install grpcio-tools
  > pip3 install --upgrade google-api-python-client --ignore-installed
```

### Step 2. Make a base proto file
##### Open a new terminal

> Basically all the examples guide with '~/grpc_handson_v2/...' path.  
> If you clone this git at the other path, use converted paths in examples.

```
> git clone ssh://git@git.mzcgroup.net:2224/dabins/grpc_handson_v2.git ~/grpc_handson_v2
> cd ~/grpc_handson_v2
```
---
### [Protocol buffers(Chapter 1)](https://git.mzcgroup.net/dabins/grpc_handson_v2/-/blob/master/1-Protocol_buffers/Chapter1.md)
### [gRPC(Chapter 2)](https://git.mzcgroup.net/dabins/grpc_handson_v2/-/blob/master/2-gRPC_server_java/Chapter2.md)