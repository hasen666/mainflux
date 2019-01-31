# LoRa Adapter
Adapter between Mainflux IoT system and [LoRa Server](https://github.com/brocaar/loraserver).

This adapter sits between Mainflux and LoRa server and just forwards the messages from one system to another via MQTT protocol, using the adequate MQTT topics and in the good message format (JSON and SenML), i.e. respecting the APIs of both systems.

LoRa Server is used for connectivity layer and data is pushed via this adapter service to Mainflux, where it is persisted and routed to other protocols via Mainflux multi-protocol message broker. Mainflux adds user accounts, application management and security in order to obtain the overall end-to-end LoRa solution.

## Configuration

The service is configured using the environment variables presented in the
following table. Note that any unset variables will be replaced with their
default values.

| Variable                         | Description                           | Default               |
|----------------------------------|---------------------------------------|-----------------------|
| MF_LORA_ADAPTER_HTTP_PORT        | Service HTTP port                     | 8180                  |
| MF_LORA_ADAPTER_LOG_LEVEL        | Log level for the Lora Adapter        | error                 |
| MF_NATS_URL                      | NATS instance URL                     | nats://localhost:4222 |
| MF_LORA_ADAPTER_MESSAGES_URL     | LoRa Server mqtt broker URL           | tcp://localhost:1883  |
| MF_LORA_ADAPTER_ROUTEMAP_URL     | Routemap database URL                 | localhost:6379        |
| MF_LORA_ADAPTER_ROUTEMAP_PASS    | Routemap database password            |                       |
| MF_LORA_ADAPTER_ROUTEMAP_DB      | Routemap instance that should be used | 0                     |
| MF_THINGS_ES_URL                 | Things service event store URL        | localhost:6379        |
| MF_THINGS_ES_PASS                | Things service event store password   |                       |
| MF_THINGS_ES_DB                  | Things service event store db         | 0                     |
| MF_LORA_ADAPTER_INSTANCE_NAME    | LoRa adapter instance name            | lora                  |

## Deployment

The service is distributed as Docker container. The following snippet provides
a compose file template that can be used to deploy the service container locally:

```yaml
version: "2"
services:
  adapter:
    image: mainflux/lora:[version]
    container_name: [instance name]
    environment:
      MF_LORA_ADAPTER_LOG_LEVEL: [Lora Adapter Log Level]
      MF_NATS_URL: [NATS instance URL]
      MF_LORA_ADAPTER_MESSAGES_URL: [LoRa Server mqtt broker URL]
      MF_LORA_ADAPTER_ROUTEMAP_URL: [Lora adapter routemap URL]
      MF_LORA_ADAPTER_ROUTEMAP_PASS: [Lora adapter routemap password]
      MF_LORA_ADAPTER_ROUTEMAP_DB: [Lora adapter routemap instance]
      MF_THINGS_ES_URL: [Things service event store URL]
      MF_THINGS_ES_PASS: [Things service event store password]
      MF_THINGS_ES_DB: [Things service event store db]
      MF_LORA_ADAPTER_INSTANCE_NAME: [LoRa adapter instance name]
```

To start the service outside of the container, execute the following shell script:

```bash
# download the latest version of the service
go get github.com/mainflux/mainflux

cd $GOPATH/src/github.com/mainflux/mainflux

# compile the lora adapter
make lora

# copy binary to bin
make install

# set the environment variables and run the service
MF_LORA_ADAPTER_LOG_LEVEL=[Lora Adapter Log Level] MF_NATS_URL=[NATS instance URL] MF_LORA_ADAPTER_MESSAGES_URL=[LoRa Server mqtt broker URL] MF_LORA_ADAPTER_ROUTEMAP_URL=[Lora adapter routemap URL] MF_LORA_ADAPTER_ROUTEMAP_PASS=[Lora adapter routemap password] MF_LORA_ADAPTER_ROUTEMAP_DB=[Lora adapter routemap instance] MF_THINGS_ES_URL=[Things service event store URL] MF_THINGS_ES_PASS=[Things service event store password] MF_THINGS_ES_DB=[Things service event store db] MF_LORA_ADAPTER_INSTANCE_NAME=[LoRa adapter instance name] $GOBIN/mainflux-lora
```

### Using docker-compose

This service can be deployed using docker containers.
Docker compose file is available in `<project_root>/docker/addons/lora-adapter/docker-compose.yml`. In order to run Mainflux lora-adapter, execute the following command:

```bash
docker-compose -f docker/addons/lora-adapter/docker-compose.yml up -d
```

## Usage

For more information about service capabilities and its usage, please check out
the [Mainflux documentation](https://mainflux.readthedocs.io/en/latest/lora/).