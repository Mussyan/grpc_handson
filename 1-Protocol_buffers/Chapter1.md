# Chapter 1. Protocol Buffers

## Step 1. Prepare the proto file and compile
```
> cd 1-Protocol_buffers
> mkdir proto
> vi proto/helloworld.proto
```

```
syntax = "proto3";

message Person {
  string name = 1;
  int32 age = 2;
  string team = 3;
}
```

```
> protoc -I=./proto --python_out=./ ./proto/helloworld.proto
```

## Step 2. Write a file with protobuf serialize
```
> vi write.py
```

```
import helloworld_pb2

person = helloworld_pb2.Person()

# Replace values! 
person.name = 'Leo'
person.age = 26
person.team = 'MaaP'
serial = person.SerializeToString()

try:
  f = open('serial','wb')
  f.write(serial)
  f.close()
  print ('file is written')
  print ('file size : ' + str(len(serial)) + ' Byte(s)')
except IOError:
  print ('file creation error')
```

```
> python3 write.py
```
    
## Step 3. Read serialize file and get data
```
> vi read.py
```

```
import helloworld_pb2

person = helloworld_pb2.Person()

try:
  f = open('serial','rb')
  person.ParseFromString(f.read())
  f.close()
  print ('name : ' + person.name)
  print ('age : ' + str(person.age))
  print ('team : ' + person.team)
except IOError:
  print ('file read error')
```

```
> python3 read.py
```
    
## Step 4. Compare with JSON format
```
> vi toJson.py
```

```
import helloworld_pb2
from google.protobuf.json_format import MessageToJson

person = helloworld_pb2.Person()

try:
  f = open('serial','rb')
  person.ParseFromString(f.read())
  f.close()
  jsonObj = MessageToJson(person)
  print (jsonObj)
  print ('size : ' + str(len(jsonObj)) + ' Byte(s)')
except IOError:
  print ('file read error')
```

```
> python3 toJson.py
```

---
## [Next chapter(Chapter 2)](https://git.mzcgroup.net/dabins/grpc_handson_v2/-/blob/master/2-gRPC_server_java/Chapter2.md)