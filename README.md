# docker-gosstrak-demo

# Synopsis
```sh
% git clone https://github.com/iomz/docker-gosstrak-demo
% cd docker-gosstrak-demo
% docker-compose up -d
```

# golemu container

## Build golemu binary
```sh
~/go/src/github.com/iomz/golemu % CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o ~/docker-gosstrak-demo/golemu/golemu .
```

## Build golemu docker image
```sh
~/docker-gosstrak-demo/golemu % docker build -t golemu -f Dockerfile.scratch .
```

## Run golemu container
```sh
~/docker-gosstrak-demo % docker run --name golemu --mount type=bind,source=<path_to_project>/docker-gosstrak-demo/data/golemu,target=/opt/golemu golemu:latest
```

# gosstrak container

## Build gosstrak-fc binary
```sh
~/go/src/github.com/iomz/gosstrak % CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o ~/docker-gosstrak-demo/gosstrak/gosstrak-fc ./cmd/gosstrak-fc
```

## Build gosstrak docker image
```sh
~/docker-gosstrak-demo/gosstrak % docker build -t gosstrak -f Dockerfile.scratch .
```

## Run gostrak container
```sh
~/docker-gosstrak-demo % docker run --name gosstrak --mount type=bind,source=<path_to_project>/docker-gosstrak-demo/data/gosstrak,target=/opt/gosstrak gosstrak:latest
```
