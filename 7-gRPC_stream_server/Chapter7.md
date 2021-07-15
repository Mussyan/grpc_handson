# Chapter 7. gRPC streaming server

## Step 1. New project
```
- Gradle
- jdk 1.8
- location : ~/grpc_handson_v2/7-gRPC_stream_server/stream_server
```

## Step 2. Setting build.gradle
> you can get the code below with the following command.  
> pbcopy < ../buildgradle/buildgradle
```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.google.protobuf:protobuf-gradle-plugin:0.8.8'
    }
}

plugins {
    id 'java'
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

sourceSets {
    src {
        main {
            java {
                srcDirs 'build/generated/source/proto/main/grpc'
                srcDirs 'build/generated/source/proto/main/java'
            }
        }
    }
}

apply plugin: 'com.google.protobuf'

repositories {
    mavenCentral()
}

dependencies {
    /**
     * gRPC
     */
    compile group: 'io.grpc', name: 'grpc-netty-shaded', version: '1.35.0'
    compile group: 'io.grpc', name: 'grpc-protobuf', version: '1.35.0'
    compile group: 'io.grpc', name: 'grpc-stub', version: '1.35.0'

    implementation 'javax.annotation:javax.annotation-api:1.3.2'
    implementation "com.google.protobuf:protobuf-java-util:3.8.0"
    compile group: 'com.google.protobuf', name: 'protobuf-java', version: '3.8.0'

    testCompile group: 'junit', name: 'junit', version: '4.12'
}

protobuf {
    protoc {
        artifact = "com.google.protobuf:protoc:3.8.0"
    }
    plugins {
        grpc {
            artifact = 'io.grpc:protoc-gen-grpc-java:1.35.0'
        }
    }
    generateProtoTasks {
        all()*.plugins {
            grpc {}
        }
    }
}
```

## Step 3. Prepare the proto file, edit and compile
```
> cd ../7-gRPC_stream_server
> ln -s ../../../../proto stream_server/src/main/proto
> vi ../proto/helloworld.proto
```

```
syntax = "proto3";

option java_multiple_files = true;

message PingPongRequest {
  string ping = 1;
}
message PingPongResponse {
  string pong = 1;
}

service PingPong {
  //unary : 한 번 호출에 한 번 응답
  rpc Register(PingPongRequest) returns (PingPongResponse);
  //client stream : 클라이언트에서 스트림으로 전달, 서버에서는 한번 응답
  rpc RegisterClientStream(stream PingPongRequest) returns (PingPongResponse);
  //server stream : 클라이언트에서 한 번 전달, 서버에서 스트림으로 응답
  rpc RegisterServerStream(PingPongRequest) returns (stream PingPongResponse);
  //bi stream : 클라이언트/서버 모두 스트림으로 응답
  rpc RegisterBiStream(stream PingPongRequest) returns (stream PingPongResponse);
}
```

##### gRPC server project IDE gradle menu > Tasks > other > generateProto

---
## Step 4. Make service implements class
##### New class 'PingPongService.java' in src/main/java

```
public class PingPongService extends PingPongGrpc.PingPongImplBase {

  //unary
  @Override
  public void register(PingPongRequest request, StreamObserver<PingPongResponse> responseObserver) {
    System.out.println("--- unary Stream ---\n" + request.toString());
    PingPongResponse response = PingPongResponse.newBuilder()
        .setPong("Pong!")
        .build();
    responseObserver.onNext(response);
    responseObserver.onCompleted();
  }

  //client stream
  @Override
  public StreamObserver<PingPongRequest> registerClientStream(StreamObserver<PingPongResponse> responseObserver) {
    StreamObserver<PingPongRequest> stream = new StreamObserver<PingPongRequest>() {
      @Override
      public void onNext(PingPongRequest pingPongRequest) {
        System.out.println("--- Client Stream ---\n" + pingPongRequest.toString());
      }

      @Override
      public void onError(Throwable throwable) {

      }

      @Override
      public void onCompleted() {
        PingPongResponse response = PingPongResponse.newBuilder()
            .setPong("Pong!")
            .build();
        responseObserver.onNext(response);
        responseObserver.onCompleted();
      }
    };
    return stream;
  }

  //server stream
  @Override
  public void registerServerStream(PingPongRequest request, StreamObserver<PingPongResponse> responseObserver) {
    System.out.println("--- Server Stream ---\n" + request.toString());

    PingPongResponse response = PingPongResponse.newBuilder()
        .setPong("Pong!")
        .build();
    responseObserver.onNext(response);
    responseObserver.onNext(response.toBuilder().setPong("Pong Pong!").build());
    responseObserver.onCompleted();
  }

  //bi stream
  @Override
  public StreamObserver<PingPongRequest> registerBiStream(StreamObserver<PingPongResponse> responseObserver) {
    StreamObserver<PingPongRequest> stream = new StreamObserver<PingPongRequest>() {
      @Override
      public void onNext(PingPongRequest pingPongRequest) {
        System.out.println("--- Bi Stream ---\n" + pingPongRequest.toString());
      }

      @Override
      public void onError(Throwable throwable) {

      }

      @Override
      public void onCompleted() {
        PingPongResponse response = PingPongResponse.newBuilder()
            .setPong("Pong!")
            .build();
        responseObserver.onNext(response);
        responseObserver.onNext(response.toBuilder().setPong("Pong Pong!").build());
        responseObserver.onCompleted();
      }
    };
    return stream;
  }
}
```

## Step 5. Make runner class & run
##### New class 'ServerRunner.java' in src/main/java

```
public class ServerRunner {

  public static void main(String[] args) throws IOException, InterruptedException {
    Server server = ServerBuilder
        .forPort(36301)
        .addService(new PingPongService()).build();

    server.start();
    System.out.println("Listening port 36301...");
    server.awaitTermination();
  }
}

```

##### Run 'ServerRunner'

## [Next chapter(Chapter 8)](https://github.com/Mussyan/grpc_handson/blob/master/8-gRPC_stream_client/Chapter8.md)
