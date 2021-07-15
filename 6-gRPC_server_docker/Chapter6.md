# Chapter 6. gRPC server with docker

## Step 1. Gradle build gRPC server
```
> cd ../2-gRPC_server_java/grpc_server
> ./gradlew build
```

## Step 2. Make Dockerfile & docker build
```
> vi Dockerfile
```

```
FROM openjdk:8-jdk-alpine

ADD ./build/libs/grpc_handson_jar.jar app.jar
ENTRYPOINT java \
-jar /app.jar
```

```
> docker build -t grpc-server:1.0 .
```

## Step 3. Run docker and test
##### Stop java gRPC server for port.
```
> docker run -p 36200:36200 --name grpc-server grpc-server:1.0
```

> If occur some port issue, try this.
>
> lsof -i :36200
>
> kill -9 ${PID}

## Step 4. Test with exist client

##### Please use
- java client(ch3) with IDE
- python client(ch5) with new terminal

---
##### Finished! Thanks for watching :)

---
##### Clean-up step... or Chapter 7
```
docker rm grpc-server
docker rmi grpc-server:1.0
cd ../../..
rm -rf grpc_handson_v2
brew uninstall protobuf
pip3 uninstall grpcio
pip3 uninstall grpcio-tools
```
## [Next chapter(Chapter 7)](https://github.com/Mussyan/grpc_handson/blob/master/7-gRPC_stream_server/Chapter7.md)
