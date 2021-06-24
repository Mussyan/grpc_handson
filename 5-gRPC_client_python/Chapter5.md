# Chapter 5. gRPC client with python

## Step 1. Prepare the proto file and compile
```
> cd ../5-gRPC_client_python
> ln -s ../proto proto
```

```
> python3 -m grpc_tools.protoc -I=../proto --python_out=./ --grpc_python_out=./ ../proto/helloworld.proto
```

## Step 2. Make client program & run
```
> vi HelloClient.py
```

```
from __future__ import print_function
import grpc
import helloworld_pb2
import helloworld_pb2_grpc

#Replace values :)
_name = "Leo"
_age = 26
_team = "MaaP"

_nums = [1, 3, 4, 5, 7]

def run():
  channel = grpc.insecure_channel('localhost:36200')
  stub = helloworld_pb2_grpc.HelloServiceStub(channel)
  helloResponse = stub.hello(helloworld_pb2.HelloRequest(name=_name, age=_age, team=_team))
  print("Hello client received: " + helloResponse.greeting)
  duplicateResponse = stub.duplicate(helloworld_pb2.DuplicateRequest(nums=_nums))
  print("Duplicate client received: " + str(duplicateResponse.isDuplicated))

if __name__ == '__main__':
    run()
```

```
> python3 HelloClient.py
```

---
## [Next chapter(Chapter 6)](https://git.mzcgroup.net/dabins/grpc_handson_v2/-/blob/master/6-gRPC_server_docker/Chapter6.md)