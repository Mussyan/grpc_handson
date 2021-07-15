# Chapter 2. gRPC Server with java

## Step 1. New project
```
- Gradle
- jdk 1.8
- location : ~/grpc_handson_v2/2-gRPC_server_java/grpc_server
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

jar {
//  enabled = true
    archivesBaseName   = "grpc_handson_jar"
    from {
        configurations.runtimeClasspath.collect { it.isDirectory() ? it : zipTree(it) }
    }
    manifest {
        attributes 'Main-Class' : "HelloServerRunner"
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
> cd ../2-gRPC_server_java
> ln -s ../../../../proto grpc_server/src/main/proto
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

service HelloService {
  rpc hello(HelloRequest) returns (HelloResponse);
}
```

##### gRPC server project IDE gradle menu > Tasks > other > generateProto

---
## Step 4. Make service implements class
##### New class 'HelloServiceImpl.java' in src/main/java

```
public class HelloServiceImpl extends HelloServiceGrpc.HelloServiceImplBase{

  @Override
  public void hello(HelloRequest request, StreamObserver<HelloResponse> responseObserver) {
    String greeting = "Hello, " + request.getName() + "(" + request.getAge() + ") from team " + request.getTeam();

    System.out.println("Remote client called a service 'hello'.");

    HelloResponse helloResponse = HelloResponse.newBuilder()
        .setGreeting(greeting)
        .build();

    responseObserver.onNext(helloResponse);
    responseObserver.onCompleted();
  }
}
```

## Step 5. Make runner class & run
##### New class 'HelloServerRunner.java' in src/main/java

```
public class HelloServerRunner {
  public static void main(String args[]) throws IOException, InterruptedException {
    Server server = ServerBuilder
        .forPort(36200)
        .addService(new HelloServiceImpl()).build();

    System.out.println("Listening port 36200...");

    server.start();
    server.awaitTermination();
  }
}
```

##### Run 'HelloServerRunner'

---
## [Next chapter(Chapter 3)](https://github.com/Mussyan/grpc_handson/blob/master/3-gRPC_client_java/Chapter3.md)