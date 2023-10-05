# Project concerning K8s, django and GCP

To configure authentication with user credentials, run the following command:

>gcloud auth login

To configure authentication with service account credentials, run the following command:

gcloud auth activate-service-account SA-ID--key-file=KEY-FILE
This will create a config file in ~/.docker/config.json

gcloud auth configure-docker HOSTNAME-LIST
EG HOSTNAME-LIST https://us-central1-docker.pkg.dev

For a system where gcloud cli is not available use this command

VERSION=2.1.14
OS=linux  # or "darwin" for OSX, "windows" for Windows.
ARCH=amd64  # or "386" for 32-bit OSs

curl -fsSL "https://github.com/GoogleCloudPlatform/docker-credential-gcr/releases/download/v${VERSION}/docker-credential-gcr_${OS}_${ARCH}-${VERSION}.tar.gz" \
| tar xz docker-credential-gcr \
&& chmod +x docker-credential-gcr && sudo mv docker-credential-gcr /usr/bin/


gcloud auth activate-service-account artifacts-k8s@production-api-enabill.iam.gserviceaccount.com --key-file=serviceacountkey.json


gcloud auth print-access-token     --impersonate-service-account artifacts-k8s@production-api-enabill.iam.gserviceaccount.com | docker login     -u oauth2accesstoken     --password-stdin https://us-central1-docker.pkg.dev


Get a service account key and then base64 encode and run this command to login

To put this in the pipeline, add the variable for the key in secrets.

cat newkey.json | docker login -u _json_key_base64 --password-stdin \
https://us-central1-docker.pkg.dev 



kubectl create secret docker-registry artifact-registry \
--docker-server=https://us-central1-docker.pkg.dev \
--docker-email=artifacts-k8s@production-api-enabill.iam.gserviceaccount.com \
--docker-username=_json_key \
--docker-password="$(cat KEY-FILE)"