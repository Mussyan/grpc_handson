# Chapter 8. gRPC streaming client

## Step 1. New project
```
- Gradle
- jdk 1.8
- location : ~/grpc_handson_v2/8-gRPC_stream_client/stream_client
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

## Step 3. Prepare the proto file and compile
```
> cd ../8-gRPC_stream_client
> ln -s ../../../../proto stream_client/src/main/proto
```

##### gRPC server project IDE gradle menu > Tasks > other > generateProto

---
## Step 4. Make new class
##### New class 'PingPongClient.java' in src/main/java

```
public class PingPongClient {
  private PingPongGrpc.PingPongBlockingStub blockingStub;
  private PingPongGrpc.PingPongStub asyncStub;

  public PingPongClient(PingPongGrpc.PingPongBlockingStub blockingStub, PingPongGrpc.PingPongStub asyncStub){
    this.blockingStub = blockingStub;
    this.asyncStub = asyncStub;
  }

  public void sendBlockingUnaryMessage(PingPongRequest pingPongRequest) {
    System.out.println("----- request -----\n" + pingPongRequest);
    PingPongResponse pingPongResponse = blockingStub.register(pingPongRequest);
    System.out.println("----- response -----\n" + pingPongResponse);
  }

  public void sendAsyncClientStreamingMessage(List<PingPongRequest> pingPongRequestList) throws InterruptedException{
    StreamObserver<PingPongResponse> responseStreamObserver = new StreamObserver<PingPongResponse>() {
      @Override
      public void onNext(PingPongResponse value) {
        System.out.println("----- response -----\n" + value.getPong());
      }

      @Override
      public void onError(Throwable t) {

      }

      @Override
      public void onCompleted() {
        System.out.println("----- on complete -----\n");
      }
    };
    StreamObserver<PingPongRequest> requestStreamObserver = asyncStub.registerClientStream(responseStreamObserver);
    for(PingPongRequest pingPongRequest : pingPongRequestList) {
      System.out.println("----- request -----\n" + pingPongRequest.getPing());
      requestStreamObserver.onNext(pingPongRequest);
    }
    requestStreamObserver.onCompleted();
    Thread.sleep(6000);
  }

  public void sendBlockingServerStreamingMessage(PingPongRequest pingPongRequest) {
    System.out.println("----- request -----\n" + pingPongRequest);
    Iterator<PingPongResponse> requestIterator;
    requestIterator = blockingStub.registerServerStream(pingPongRequest);
    requestIterator.forEachRemaining((pingPongResponse) -> {
      System.out.println("----- response -----\n" + pingPongResponse);
    });
  }

  public void sendBiDirectionalStreamingMessage(List<PingPongRequest> pingPongRequestList) throws InterruptedException {
    StreamObserver<PingPongResponse> responseStreamObserver = new StreamObserver<PingPongResponse>() {
      @Override
      public void onNext(PingPongResponse value) {
        System.out.println("----- Response -----\n" + value.getPong());
      }

      @Override
      public void onError(Throwable t) {

      }

      @Override
      public void onCompleted() {
        System.out.println("----- Finished bi-streaming -----\n");
      }
    };
    StreamObserver<PingPongRequest> requestStreamObserver = asyncStub.registerBiStream(responseStreamObserver);
    for(PingPongRequest pingPongRequest : pingPongRequestList) {
      System.out.println("----- request -----\n" + pingPongRequest.getPing());
      requestStreamObserver.onNext(pingPongRequest);
    }
    requestStreamObserver.onCompleted();
    Thread.sleep(6000);
  }
}
```
## Step 5. Make runner class & run
##### New class 'ClientRunner.java' in src/main/java
```
public class ClientRunner {

  public static void main(String[] args) throws InterruptedException{
    ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 36301)
        .usePlaintext()
        .build();
    PingPongGrpc.PingPongBlockingStub blockingStub = PingPongGrpc.newBlockingStub(channel);
    PingPongGrpc.PingPongStub asyncStub = PingPongGrpc.newStub(channel);

    PingPongClient pingPongClient = new PingPongClient(blockingStub, asyncStub);

    PingPongRequest pingPongRequest = PingPongRequest.newBuilder()
        .setPing("Ping!")
        .build();

    PingPongRequest pingPongRequest2 = PingPongRequest.newBuilder()
        .setPing("Ping Ping!")
        .build();

    List<PingPongRequest> pingPongRequestList = new ArrayList<>();
    pingPongRequestList.add(pingPongRequest);
    pingPongRequestList.add(pingPongRequest2);

    pingPongClient.sendBlockingUnaryMessage(pingPongRequest);

//    pingPongClient.sendAsyncClientStreamingMessage(pingPongRequestList);

//    pingPongClient.sendBlockingServerStreamingMessage(pingPongRequest);

//    pingPongClient.sendBiDirectionalStreamingMessage(pingPongRequestList);

    channel.shutdown();
  }
}
```


##### Run 'HelloClientRunner'

---
## Stream finished