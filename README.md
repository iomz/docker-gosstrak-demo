# docker-gosstrak-demo

This repository contains a docker-compose for creating a working demo sample for [golemu](https://github.com/iomz/golemu) (LLRP Emulator), [gosstrak-fc](https://github.com/iomz/gosstrak) (Filtering & Collection middleware), and the visualization of LLRP traffic by [Grafana](https://grafana.com/) and [InfluxDB](https://grafana.com).

![Demo on Grafana](https://i.imgur.com/4mgHa5K.gif)

## What does the demo do?

The `docker-compose.yml` will spin up 4 docker containers.

- golemu: Run LLRP emulator with the gob files placed in `data/golemu/dataset`. golemu iterates through each `.gob` file and repeat the cycle endlessly.
- gosstrak: Run Filtering & Collection middleware to receive the RFID read events from the emulator in LLRP. It filters the tags based on `data/gosstrak/ecspec.csv`. The `ecspec.csv` contains prefix URN of interested ID patterns and its corresponding notification URI. The ID patterns are aggregated with the notification URI; i.e., the first entry is the URI and the rest of the line is the patterns.
- influxdb: A InfluxDB instance which receives the performance monitoring directly from gosstrak.
- grafana: Draw realtime plots of gosstrak performance with the InfluxDB instance.

The latest version of [iomz/gosstrak](https://cloud.docker.com/u/iomz/repository/docker/iomz/gosstrak) docker image on Docker Hub is a custom version of gosstrak which dynamically adopts the efficient filtering algorithm depending on the statistics of event traffic.

## How to setup the demo

1. [Install Docker](https://docs.docker.com/install/) and [docker-compose](https://docs.docker.com/compose/install/) on your system.

2. Clone this repository.

3. (Optional) Prepare golemu and gosstrak docker images if you want to run different versions of them. See [Containers](#containers) section.

4. Download and untar the sample RFID tag dataset from https://github.com/iomz/docker-gosstrak-demo/releases/download/v0.0.1/dataset.tgz then place the `.gob` files in `data/golemu/dataset`.

5. Download the sample filtering rules from https://github.com/iomz/docker-gosstrak-demo/releases/download/v0.0.1/ecspec.csv and place it as `data/gosstrak/ecspec.csv`.

6. Start the containers and wait a while for all the containers to start up. You can then access the Grafana dashboard at `http://localhost:3000` (User/Pass: admin/gosstrak) – navigate to the `gosstrak` dashboard.
```
% cd docker-gosstrak-demo
% docker-compose up
```

## The containers

The below assumes this repository is cloned to `~/docker-gosstrak-demo`.

### golemu

Build the single binary golemu from the source and put build the docker image with it.

1. Build a golemu binary and place it
```sh
$GOPATH/src/github.com/iomz/golemu % CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o ~/docker-gosstrak-demo/golemu/golemu .
```

2. Build a golemu docker image
```sh
~/docker-gosstrak-demo/golemu % docker build -t golemu -f Dockerfile.scratch .
```

### gosstrak

Likewise in golemu.

1. Build gosstrak-fc binary
```sh
$GOPAH/src/github.com/iomz/gosstrak % CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o ~/docker-gosstrak-demo/gosstrak/gosstrak-fc ./cmd/gosstrak-fc
```

2. Build gosstrak docker image
```sh
~/docker-gosstrak-demo/gosstrak % docker build -t gosstrak -f Dockerfile.scratch .
```

## Run only a golemu/gosstrak container

### Run gosstrak container

```sh
~/docker-gosstrak-demo % docker run --name gosstrak --mount type=bind,source=<path_to_project>/docker-gosstrak-demo/data/gosstrak,target=/opt/gosstrak gosstrak:latest
```

### Run golemu container

```sh
~/docker-gosstrak-demo % docker run --name golemu --mount type=bind,source=<path_to_project>/docker-gosstrak-demo/data/golemu,target=/opt/golemu golemu:latest
```
## License

MIT

## Author

Iori Mizutani (a.k.a. iomz)
