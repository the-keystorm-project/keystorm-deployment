# Keystorm deployment guide
This repository contains Docker compose file to easily setup [Keystorm](https://github.com/the-keystorm-project/keystorm) via Docker. Keystorm stack is composed of Keystorm application (Ruby on Rails API application), Apache server and Memcached instance. This repository should serve as an example of how to run Keystorm via Docker. To run in a production environment it should be modified for needs of the underlying infrastructure.

## Requirements
* Docker version >= 17.06.0 with API version >= 1.25
* OpenNebula >= 5.2

### Docker setup
Docker must by in swarm mode to support secrets and stacks. For testing purposes you can set Docker to swarm mode locally by running:
```bash
docker swarm init
```
For further information about swarm mode read [https://docs.docker.com/engine/swarm/](https://docs.docker.com/engine/swarm/).

## Configuration
### Docker secrets
Both Keystorm and Apache server expects confidential data as Docker secrets. Here is the list of required secrets:
* **keystorm-opennebula-auth** - OpenNebula secret (e.g. *oneadmin:opennebula*)
* **keystorm-token-cipher** - Keystorm token cipher (e.g. *AES-128-CBC*)
* **keystorm-token-key** - Keystorm token key (must be 16 characters long, e.g. `head -c 12 /dev/urandom | base64 -w 0`)
* **keystorm-token-iv** - Keystorm token initialization vector (must be 16 characters long, e.g. `head -c 12 /dev/urandom | base64 -w 0`)
* **apache-keystorm-oidc-client-secret** - OIDC client secret
* **apache-keystorm-oidc-crypto-passphrase** - OIDC crypto passphrase

On how to manage Docker secrets read [https://docs.docker.com/engine/swarm/secrets/](https://docs.docker.com/engine/swarm/secrets/).

### Environment variables
Keystorm and Apache server are configured via environment variables:
* **KEYSTORM_KEYSTORM_VERSION** - Version of `keystorm/keystorm` Docker image
* **KEYSTORM_APACHE_KEYSTORM_VERSION** - Version of `keystorm/apache-keystorm` Docker image
* **KEYSTORM_OPENNEBULA_ENDPOINT** - OpenNebula RPC2 endpoint
* **KEYSTORM_EXPIRATION_WINDOW** - Token expiration time
* **KEYSTORM_KEYSTORM_ENDPOINT** - Keystorm endpoint
* **KEYSTORM_OIDC_METADATA_URL** - OIDC provider configuration endpoint
* **KEYSTORM_OIDC_CLIENT_ID** - OIDC client ID
* **KEYSTORM_OIDC_INTROSPECTION_ENDPOINT** - OIDC introspection endpoint
* **KEYSTORM_VOMS_CONFIGURATION** - Supported VOMS VOs (in form of JSON)
* **KEYSTORM_DEBUG** - When set to `debug` both Keystorm and Apache server will run in debug mode

To easily set environment variables modify file `deployment.sh` as you need and run
```bash
source ./deployment.sh
```

## Deployment
Deploy Keystorm and Apache server as a Docker stack:
```bash
docker stack deploy -c docker-compose.yml keystorm
```
## Debugging
Both Docker images are logging to standard output so to see the logs simply run `docker logs <container ID>`. To obtain more information you can run both containers in debug mode by setting environment variable `KEYSTORM_DEBUG` to `debug` prior to deploying the stack.

## Teardown
Docker stack can be shut down as
```bash
docker stack rm keystorm
```
