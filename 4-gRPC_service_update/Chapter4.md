# Chapter 4. Update gRPC service

## Step 1. Update proto file
```
> vi ../proto/helloworld.proto
```

```
syntax = "proto3";

option java_multiple_files = true;

message HelloRequest {
  string name = 1;
  int32 age = 2;
  string team = 3;
}

message HelloResponse {
  string greeting = 1;
}

message DuplicateRequest {
  repeated int32 nums = 1;
}

message DuplicateResponse {
  bool isDuplicated = 1;
}

service HelloService {
  rpc hello(HelloRequest) returns (HelloResponse);
  rpc duplicate(DuplicateRequest) returns (DuplicateResponse);
}
```

## Step 2. Update gRPC server
Stop server

Run generateProto task in gRPC server

Update service at 'HelloServiceImpl.java' in gRPC server

```
@Override
  public void duplicate(DuplicateRequest request,
      StreamObserver<DuplicateResponse> responseObserver) {
    boolean isDuplicated;
    List<Integer> nums = request.getNumsList();
    Set<Integer> tmpSet = new HashSet<>(nums);

    System.out.println("Remote client called a service 'duplicate'.");

    isDuplicated = nums.size() > tmpSet.size();

    DuplicateResponse duplicateResponse = DuplicateResponse.newBuilder()
        .setIsDuplicated(isDuplicated)
        .build();

    responseObserver.onNext(duplicateResponse);
    responseObserver.onCompleted();
  }
```

Run 'HelloServerRunner'

## Step 3. Update gRPC client
Run generateProto task in client

Make new request at 'HelloClientRunner.java' in gRPC client

```
List<Integer> nums = Arrays.asList(1, 3, 5, 6 ,7);
DuplicateResponse duplicateResponse = stub.duplicate(DuplicateRequest.newBuilder()
    .addAllNums(nums)
    .build());
String message = duplicateResponse.getIsDuplicated() ? "Duplicate!" : "Not duplicate!";

System.out.println("\n-----------------------------------------");
System.out.println(message);
System.out.println("-----------------------------------------\n");
```

Run 'HelloClientRunner'

---
## [Next chapter(Chapter 5)](https://github.com/Mussyan/grpc_handson/blob/master/5-gRPC_client_python/Chapter5.md)