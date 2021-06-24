# Chapter 3. gRPC client with java

## Step 1. New project
```
- Gradle
- jdk 1.8
- location : ~/grpc_handson_v2/3-gRPC_client_java/grpc_client
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
> cd ../3-gRPC_client_java
> ln -s ../../../../proto grpc_client/src/main/proto
```

##### gRPC server project IDE gradle menu > Tasks > other > generateProto

---
## Step 4. Make runner class & run
##### New class 'HelloClientRunner.java' in src/main/java

```
public class HelloClientRunner {
  public static void main(String[] args) {
    ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 36200)
        .usePlaintext()
        .build();

    HelloServiceGrpc.HelloServiceBlockingStub stub
        = HelloServiceGrpc.newBlockingStub(channel);

    HelloResponse helloResponse = stub.hello(HelloRequest.newBuilder()
        .setName("Leo")   //Replace to ur name :)
        .setAge(26)       //Replace to ur age :)
        .setTeam("MaaP")  //Replace to ur team :)
        .build());

    System.out.println("\n-----------------------------------------");
    System.out.print(helloResponse);
    System.out.println("-----------------------------------------\n");

    channel.shutdown();
  }
}
```
##### Run 'HelloClientRunner'

---
## [Next chapter(Chapter 4)](https://git.mzcgroup.net/dabins/grpc_handson_v2/-/blob/master/4-gRPC_service_update/Chapter4.md)