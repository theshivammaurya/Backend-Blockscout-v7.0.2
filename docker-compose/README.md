# Docker-compose configuration

Runs Blockscout locally in Docker containers with [docker-compose](https://github.com/docker/compose).

## Prerequisites

- Docker v20.10+
- Docker-compose 2.x.x+
- Running Ethereum JSON RPC client

## TO Run the Separate Backend And Frontend - Below command is for to run Backend 

```bash
cd ./docker-compose 
FRONT_PROXY_PASS=http://host.docker.internal:3000/ docker compose -f external-frontend.yml up -d 
```

## For logs use command 

```bash
docker logs -f user_ops_indexer

docker logs -f stats 

docker compose logs backend > my_backend_logs.txt      -- this command is for full backend and also create .txt file

docker logs -f backend

docker ps -a


```

- Postgres 14.x database, which will be available at port 7432 on the host machine.
- Redis database of the latest version.
- Blockscout backend with api at /api path.
- Nginx proxy to bind backend, frontend and microservices.
- Blockscout explorer at http://localhost.

**Note for Linux users**: Linux users need to run the local node on http://0.0.0.0/ rather than http://127.0.0.1/

## Configs for different Ethereum clients

The repo contains built-in configs for different JSON RPC clients without need to build the image.

| __JSON RPC Client__    | __Docker compose launch command__ |
| -------- | ------- |
| Erigon  | `docker-compose -f erigon.yml up -d`    |
| Geth (suitable for Reth as well) | `docker-compose -f geth.yml up -d`     |
| Geth Clique    | `docker-compose -f geth-clique-consensus.yml up -d`    |
| Nethermind, OpenEthereum    | `docker-compose -f nethermind.yml up -d`    |
| Ganache    | `docker-compose -f ganache.yml up -d`    |
| HardHat network    | `docker-compose -f hardhat-network.yml up -d`    |

- Running only explorer without DB: `docker-compose -f external-db.yml up -d`. In this case, no db container is created. And it assumes that the DB credentials are provided through `DATABASE_URL` environment variable on the backend container.
- Running explorer with external backend: `docker-compose -f external-backend.yml up -d`
- Running explorer with external frontend: `FRONT_PROXY_PASS=http://host.docker.internal:3000/ docker-compose -f external-frontend.yml up -d`
- Running all microservices: `docker-compose -f microservices.yml up -d`
- Running only explorer without microservices: `docker-compose -f no-services.yml up -d`

All of the configs assume the Ethereum JSON RPC is running at http://localhost:8545.

In order to stop launched containers, run `docker-compose -f config_file.yml down`, replacing `config_file.yml` with the file name of the config which was previously launched.

You can adjust BlockScout environment variables:

- for backend in `./envs/common-blockscout.env`
- for frontend in `./envs/common-frontend.env`
- for stats service in `./envs/common-stats.env`
- for visualizer in `./envs/common-visualizer.env`
- for user-ops-indexer in `./envs/common-user-ops-indexer.env`

Descriptions of the ENVs are available

- for [backend](https://docs.blockscout.com/setup/env-variables)
- for [frontend](https://github.com/blockscout/frontend/blob/main/docs/ENVS.md).

## Running Docker containers via Makefile

Prerequisites are the same, as for docker-compose setup.

Start all containers:

```bash
cd ./docker
make start
```

Stop all containers:

```bash
cd ./docker
make stop
```

***Note***: Makefile uses the same .env files since it is running docker-compose services inside.
