# Example Voting App

A simple distributed application running across multiple Docker containers that demonstrates a microservices architecture.

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/yourusername/voting-app)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

## Overview

This application showcases a basic voting system where users can vote for one of two options. The architecture demonstrates how multiple services can work together using different technologies.

## Architecture

![Architecture diagram](architecture.excalidraw.png)

- A front-end web app in [Python](/vote) which lets you vote between two options
- A [Redis](https://hub.docker.com/_/redis/) queue which collects new votes
- A [.NET](/worker/) worker which consumes votes and stores them in…
- A [Postgres](https://hub.docker.com/_/postgres/) database backed by a Docker volume
- A [Node.js](/result) web app which shows the results of the voting in real time

## Prerequisites

Download [Docker Desktop](https://www.docker.com/products/docker-desktop) for Mac or Windows. [Docker Compose](https://docs.docker.com/compose) will be automatically installed. On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).

This solution uses Python, Node.js, .NET, with Redis for messaging and Postgres for storage.

### Running the App

Run in this directory to build and run the app:

```shell
docker compose up
```

The `vote` app will be running at [http://localhost:8080](http://localhost:8080), and the `results` will be at [http://localhost:8081](http://localhost:8081).

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:

```shell
docker swarm init
```

Once you have your swarm, in this directory run:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

## Run the app in Kubernetes

The folder `k8s-specifications` contains the YAML specifications of the Voting App's services.

Run the following command to create the deployments and services. Note it will create these resources in your current namespace (`default` if you haven't changed it.)

```shell
kubectl create -f k8s-specifications/
```

The `vote` web app is then available on port 31000 on each host of the cluster, the `result` web app is available on port 31001.

To remove them, run:

```shell
kubectl delete -f k8s-specifications/
```

## Development

### Directory Structure

- `vote` - Front-end voting application written in Python
- `result` - Results visualization app built with Node.js
- `worker` - .NET worker that processes votes from Redis and stores them in Postgres
- `k8s-specifications` - Kubernetes deployment files
- `healthchecks` - Docker health check scripts for Redis and Postgres
- `seed-data` - Utility to generate test voting data

### Modifying Components

Each component has its own Dockerfile that you can modify if needed. Make changes to the application code and rebuild the containers using:

```shell
docker compose build
docker compose up
```

## Notes

The voting application only accepts one vote per client browser. It does not register additional votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to
deal with them in Docker at a basic level.

## Advanced Configuration

### Environment Variables

Each service can be configured using environment variables. Check the respective Dockerfile or service definition for the specific variables available.

### Scaling Services

You can scale services using Docker Compose or Kubernetes:

```shell
# With Docker Compose
docker compose up --scale vote=3 --scale worker=3

# With Kubernetes
kubectl scale deployment/vote --replicas=3
kubectl scale deployment/worker --replicas=3
```

## Troubleshooting

### Common Issues

1. **Services not starting properly**: Check logs with `docker compose logs [service_name]`
2. **Database connection issues**: Ensure Postgres is running with `docker compose ps`
3. **Port conflicts**: Make sure ports 8080 and 8081 are available on your host

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Docker for providing excellent documentation and examples
- All contributors who have helped improve this example application
