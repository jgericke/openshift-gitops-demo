# OpenShift GitOps Demo

Demonstrate GitOps patterns using OpenShift and OpenShift GitOps via ACM

## Setup

These steps are for multi-arch (running off a macbook m3).

### Create developer golden image (python 3.12)

1. Authenticate to the OCPP demo environment's Quay registry:

```
export QUAY_URL="quay-<demo>.apps.cluster-<fqdn>"
export QUAY_USER="<demo-env-quay-admin-user>"
export QUAY_PASSWORD="<demo-env-quay-admin-password>"

podman login --username "${QUAY_USERNAME}" --password "${QUAY_PASSWORD}" "${QUAY_URL}"
```

2. Localise the developer golden image to the demo Quay registry

```
podman pull --arch amd64 docker.io/library/python:3.12-alpine
podman tag docker.io/library/python:3.12-alpine $QUAY_URL/$QUAY_USER/python-alpine-golden:0.1
podman push $QUAY_URL/$QUAY_USER/python-alpine-golden:0.1
```

### Build the demo applications

The applications and their respective Dockerfiles are located within apps directory of this repository.

Note that build and run images for the `frontend` and `payment-processor` apps have been updated to use the developer golden image.

**NB:** To work in your own environment, please be sure to update the Dockerfile image reference to use your Quay regristry's FQDN and repository path.

Original (frontend and payment-processor):

```
FROM python:3.12-alpine AS build
...
FROM python:3.12-alpine AS run
```

Updated:

```
FROM quay-<demo>.apps.cluster-<fqdn>/<quay-user>/python-alpine-golden:0.1 AS build
...
FROM quay-<demo>.apps.cluster-<fqdn>/<quay-user>/python-alpine-golden:0.1 AS run
```

1. Build and push the frontend, payment-processor adnd database images.

Note, that I am only building for amd64 (this will default to arm64 on my machine and be incompatible with the OCP runtime architecture):

Frontend:
```
cd apps/frontend
podman build --platform linux/amd64 --manifest $QUAY_URL/$QUAY_USER/frontend:0.1 .
podman push $QUAY_URL/$QUAY_USER/frontend:0.1
```

Payment-processor:
```
cd apps/payment-processor
podman build --platform linux/amd64 --manifest $QUAY_URL/$QUAY_USER/payment-processor:0.1 .
podman push $QUAY_URL/$QUAY_USER/payment-processor:0.1
```

Database:
```
podman build --platform linux/amd64 --manifest $QUAY_URL/$QUAY_USER/database:0.1 .
podman push $QUAY_URL/$QUAY_USER/database:0.1
```

2. Navigate to the repository settings for each demo repository within Quay (Gear Icon). Under 'Repository Visibility', select 'Make Public' to ensure these images are pullable.