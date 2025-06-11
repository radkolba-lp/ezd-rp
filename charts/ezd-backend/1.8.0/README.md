## General

> **Note:**
> Chart ezd-backend was tested with chart version up to 21.11.11 (application version up to 1.2025.21.11).

### Are you looking for more information?

1. Based on: https://github.com/linuxpolska/ezd-rp
2. Documentation: https://github.com/linuxpolska/ezd-rp/blob/main/README.md
3. Chart Source: https://linuxpolska.github.io/ezd-rp


## Before Installation

#### Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure

## After Installation

> **Note:**
> Copy, write down or memorize notes from helm installation output. It will be necessary for EZD RP frontend installation.

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

```bash
RELEASE_NAMESPACE=example
CHART_VERSION=1.8.0
```

### Go go helm

```bash
cat << EOF > /tmp/values.yaml

EOF 

helm -n ${RELEASE_NAMESPACE} upgrade --install ezd-backend-release \
--repo https://linuxpolska.github.io/ezd-rp \
ezd-backend \
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
helm -n ${RELEASE_NAMESPACE} uninstall ezd-backend-release
```

## [GUI Installation](https://github.com/linuxpolska/ezd-rp/blob/main/INSTALLATION_GUI.md)

1. Log in your Rancher instance.

2. Go to cluster of your choice.

3. Go to `Apps > Repositories` and ensure that repo https://linuxpolska.github.io/ezd-rp is `Active`.

4. Go to `Apps > Charts`. Filter for EZD RP Charts.

5. Select  `EZD RP Backend (1/2) - Operators`. Click on `Install` button.

6. 
