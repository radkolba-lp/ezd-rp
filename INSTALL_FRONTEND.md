## General

### Are you looking for more information?

1. Documentation: https://github.com/linuxpolska/ezd-rp/blob/main/README.md
2. Chart Source: https://hub.eadministracja.nask.pl/chartrepo/ezdrp


## Before Installation

> **Note:**
> Use notes from ezd-backend helm installation output.
>
> OR
>
> Use file /tmp/ezd-pass.sh created in ezd-backend helm installation for CLI.

#### Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure

## After Installation

> **Note:**
> no action required

## Before Upgrade

> **Note:**
> no action required

## After Upgrade

> **Note:**
> no action required


## Tips and Tricks

> **Note:**
> List all releases using `helm list`

## Known Issues

> **Note:**
> Notify us: https://github.com/linuxpolska/ezd-rp/issues

## CLI installation

### Preparation

> **Note:**
> Installation of the EZD RP frontend requires a TLS certificate. Example creation is described [here](README_TLS.md)

```bash
CERTIFICATE_NAME=example.domain.name.crt
CERTIFICATE_KEY=example.domain.name.key
RELEASE_NAMESPACE=example
CHART_VERSION=1.8.0

cat <<EOF > /tmp/ezd-vars.sh
# Desired namespace where ezd-rp will be installed
export K8S_NAMESPACE=ezd-rp
# Set the name of the domain where ezdrp will exist
export APP_DOMAIN=
# Set environmental variable for storage class - to get available run: "kubectl get storageclass"
export K8S_SC=
# Random it by default or set own password
export APP_USER_PASSWD=$(openssl rand -hex 10)

# Default values
export POSTGRES_HOST=lp-backend-postgresql-rw
export RABBITMQ_HOST=lp-backend-rabbitmq
export REDIS_HOST=lp-backend-redis
export REDIS_APPEND_HOST=lp-backend-redis-append
export RABBITMQ_PORT=5672
export REDIS_PORT=6379
export REDIS_APPEND_PORT=6379
EOF

source /tmp/ezd-vars.sh
# ezd-pass.sh file should exists from ezd-frontend installation
source /tmp/ezd-pass.sh
```

### Go go helm

```bash
cat <<EOF > /tmp/values.yaml
network:
  ingressName: nginx
cloudadmin:
  relationaldb:
    connectionstring:
      archiwum: Host=${POSTGRES_HOST};Port=5432;Database=archiwum;Username=${PSQL_USER};Password=${PSQL_PASSWD}
      ezdrp: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp;Username=${PSQL_USER};Password=${PSQL_PASSWD}
      ezdrpodczyt: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp_odczyt;Username=${PSQL_USER};Password=${PSQL_PASSWD}
      kuip: Host=${POSTGRES_HOST};Port=5432;Database=ezdrp;Username=${PSQL_USER};Password=${PSQL_PASSWD}
    connectiontype:
      archiwum: POSTGRESQL
      ezdrp: POSTGRESQL
      ezdrpodczyt: POSTGRESQL
      kuip: POSTGRESQL
domainInfo:
  cert_info: true
  cert_name: ezdrp-cert
  name: ${APP_DOMAIN}
email:
  active: false
ezdrpApi:
  persistence:
    storageClass: ${K8S_SC}
filerepository:
  persistence:
    storageClass: ${K8S_SC}
rabbitExt:
  user: ${RABBITMQ_USER}
  password: ${RABBITMQ_PASSWD}
  host: ${RABBITMQ_HOST}
  port: ${RABBITMQ_PORT}
redisAppendExt:
  isCluster: false
  password: ${REDIS_PASSWD}
  host: ${REDIS_APPEND_HOST}
  port: ${REDIS_APPEND_PORT}
redisExt:
  isCluster: false
  password: ${REDIS_PASSWD}
  host: ${REDIS_HOST}
  port: ${REDIS_PORT}
ssoIdentityServer:
  persistence:
    storageClass: ${K8S_SC}
wpeRest:
  persistence:
    storageClass: ${K8S_SC}
EOF

# Prepare Your TLS certificate (from previous instruction)
cat certs/$CERTIFICATE_NAME.crt certs/ca.crt > certs/chain.crt

kubectl -n ${K8S_NAMESPACE} create secret tls ezdrp-cert --cert=certs/chain.crt --key=certs/$CERTIFICATE_KEY.key

helm -n ${RELEASE_NAMESPACE} upgrade --install ezd-frontend-release \
--repo https://hub.eadministracja.nask.pl/chartrepo/ezdrp \
nask-ezdrp-ha \
-f /tmp/values.yaml \
--version ${CHART_VERSION} \
--create-namespace
```

### Validation and Testing

```bash
kubectl -n ${RELEASE_NAMESPACE} get po
helm -n ${RELEASE_NAMESPACE} list
```

## CLI removing

```bash
helm -n ${RELEASE_NAMESPACE} uninstall ezd-frontend-release
```

## GUI Installation
If You want to install ezd-frontend via GUI, please follow [this instruction](../../../INSTALL_VIA_GUI.md).