# Project concerning K8s, django and GCP

To configure authentication with user credentials, run the following command:


<h2>1. To configure authentication with service account credentials, run the following command</h2>

```python
gcloud auth activate-service-account SA-ID--key-file=KEY-FILE
```

This will create a config file in ~/.docker/config.json

```python
gcloud auth configure-docker HOSTNAME-LIST
```
Eg. HOSTNAME-LIST https://{GCP-REGION}-docker.pkg.dev

<h2>2. For a system where gcloud cli is not available use this command</h2>

<code>
VERSION=2.1.14
OS=linux  # or "darwin" for OSX, "windows" for Windows.
ARCH=amd64  # or "386" for 32-bit OSs

curl -fsSL "https://github.com/GoogleCloudPlatform/docker-credential-gcr/releases/download/v${VERSION}/docker-credential-gcr_${OS}_${ARCH}-${VERSION}.tar.gz" \
| tar xz docker-credential-gcr \
&& chmod +x docker-credential-gcr && sudo mv docker-credential-gcr /usr/bin/
</code>


<h2>3. Activate service account to authenticate with Artifact registry use this gcloud cli command. Also make sure this SA is having necessary permissions</h2>
```python
gcloud auth activate-service-account {SA-user}@{PROJECT_ID}.iam.gserviceaccount.com --key-file={key_name}.json
```

This uses token which expires in 1 hour.
```python
gcloud auth print-access-token     --impersonate-service-account {SA-USER}@{PROJECT_ID}.iam.gserviceaccount.com | docker login     -u oauth2accesstoken     --password-stdin https://{REGION}-docker.pkg.dev
```


<h2>4. Create dedicated service accounts that are only used to interact with repositories.
Get a service account key and then base64 encode and run this command to login</h2>

To put this in the pipeline, add the variable for the key in secrets.
```python
cat newkey.json | docker login -u _json_key_base64 --password-stdin \
https://us-central1-docker.pkg.dev 
```


<h1>Create GKE Autopilot cluster and configure secret to use Artifact registry>h1>

```python
gcloud container clusters create {Cluster_name}
```

<h3>Create a secret to store artifact_registry as a registry which will later be used to pull the docker image.<h3>
```python
kubectl create secret docker-registry artifact-registry \
--docker-server=https://{REGION}-docker.pkg.dev \
--docker-email={SA_ID}@{PROJECT_ID}.iam.gserviceaccount.com \
--docker-username=_json_key \
--docker-password="$(cat KEY-FILE)"
```


Build and push the docker image to Artifact repository manually. 

<code>docker build -t us-central1-docker.pkg.dev/production-api-enabill/artifact-k8s/django-k8s:latest .</code>

<code>docker push us-central1-docker.pkg.dev/production-api-enabill/artifact-k8s/django-k8s --all-tags<code>


<h1>Automate using GCP CloudBuild service</h1>
1. Clone the code from GCP repo
2. Build container image
3. Pushes the image to Artifact Registry

run the following gcloud command

```python 
gcloud build submit 
```


docker push us-central1-docker.pkg.dev/production-api-enabill/artifact-k8s/django-k8s --all-tags