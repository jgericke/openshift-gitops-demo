# OpenShift GitOps Demo

Demonstrate GitOps patterns using OpenShift and OpenShift GitOps via ACM

## Localise demo-apps

1. Clone the repository:
```
git clone https://github.com/mfosterrox/demo-apps.git demo-apps
```
## Create Developer Golden Image

1. Authenticate to demo environment Quay

```
source .env
bin/quay-login.sh
```

2. Localise, tag and push the python:3.12-alpine golden image

```
podman pull python:3.12-alpine
podman tag docker.io/library/python:3.12-alpine $QUAY_URL/$QUAY_USER/python-alpine-golden:0.1

# See tagged image:
podman images | grep python-alpine-golden

podman push $QUAY_URL/$QUAY_USER/python-alpine-golden:0.1
```

2. Localise, tag and push the python:3.12-alpine golden image

```
podman pull python:3.12-alpine
podman tag docker.io/library/python:3.12-alpine $QUAY_URL/$QUAY_USER/python-alpine-golden:0.1

# See tagged image:
podman images | grep python-alpine-golden

podman push $QUAY_URL/$QUAY_USER/python-alpine-golden:0.1
```

## Build the patient portal frontend

1. Review demo app to use with golden image:

```
ls demo-apps/app-images/frontend/

cat demo-apps/app-images/frontend/Dockerfile
```

2. Update Dockerfile to leverage golden image

```
FROM quay-ntpc5.apps.cluster-ntpc5.ntpc5.sandbox470.opentlc.com/quayadmin/python-alpine-golden:0.1 AS build
#FROM python:3.12-alpine AS build
...
FROM quay-ntpc5.apps.cluster-ntpc5.ntpc5.sandbox470.opentlc.com/quayadmin/python-alpine-golden:0.1 AS run
# FROM python:3.12-alpine AS run
```

3. Build the Docker image using podman from the frontend directory. The -t flag tags the image with a version (0.1) and a registry URL.

```
cd demo-apps/app-images/frontend/
podman build -t $QUAY_URL/$QUAY_USER/frontend:0.1 .
podman push $QUAY_URL/$QUAY_USER/frontend:0.1
```

4. Perform the same for the supporting services (processor and database).

```
podman pull quay.io/skupper/patient-portal-database
podman tag quay.io/skupper/patient-portal-database $QUAY_URL/$QUAY_USER/patient-portal-database:0.1
podman push $QUAY_URL/$QUAY_USER/patient-portal-database:0.1
podman tag quay.io/skupper/patient-portal-payment-processor $QUAY_URL/$QUAY_USER/patient-portal-payment-processor:0.1
podman pull quay.io/skupper/patient-portal-payment-processor
podman tag quay.io/skupper/patient-portal-payment-processor $QUAY_URL/$QUAY_USER/patient-portal-payment-processor:0.1
podman push $QUAY_URL/$QUAY_USER/patient-portal-payment-processor:0.1
```

5. Make the image repositories (frontend, patient-portal-database & patient-portal-payment-processor) public on demo environment Quay

Navigate to repository -> Settings -> "Make Public"



