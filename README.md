# Project concerning K8s, django and GCP

To configure authentication with user credentials, run the following command:

Build this project

1. Git clone the repository
2. Install virtualenv <code>virtualenv -p python3 .</code>
3. Install django requirements <code>pip install -r requirements.txt</code>
4. Add the env variable 
5. For Production, we will use managed postgresql server so try to put the env variable separetely and add it to the kubernetes secrets using <code>kubectl create secret generic djang-k8s-web-prod-env --from-env-file=web/.env.prod</code>
6. Make sure to include .env.prod file in .gitignore and .dockerignore
7. We will use Google cloud source repository to build CI/CD pipeline using Cloud build.
8. Make sure to have permissions for your service account ( read/write access to artifact repository) and create key to authenticate with docker login and kubernetes deploy using gcloud command.

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


<code>VERSION=2.1.14
OS=linux  # or "darwin" for OSX, "windows" for Windows.
ARCH=amd64  # or "386" for 32-bit OSs</code>

curl -fsSL "https://github.com/GoogleCloudPlatform/docker-credential-gcr/releases/download/v${VERSION}/docker-credential-gcr_${OS}_${ARCH}-${VERSION}.tar.gz" \
| tar xz docker-credential-gcr \
&& chmod +x docker-credential-gcr && sudo mv docker-credential-gcr /usr/bin/


<h2>3. Activate service account to authenticate with Artifact registry use this gcloud cli command. Also make sure this SA is having necessary permissions</h2>


<code>gcloud auth activate-service-account {SA-user}@{PROJECT_ID}.iam.gserviceaccount.com --key-file={key_name}.json</code>

This uses token which expires in 1 hour.

<code>gcloud auth print-access-token     --impersonate-service-account {SA-USER}@{PROJECT_ID}.iam.gserviceaccount.com | docker login     -u oauth2accesstoken     --password-stdin https://{REGION}-docker.pkg.dev
</code>

<h2>4. Create dedicated service accounts that are only used to interact with repositories.
Get a service account key and then base64 encode and run this command to login</h2>

To put this in the pipeline, add the variable for the key in secrets.

<code>cat newkey.json | docker login -u _json_key_base64 --password-stdin \
https://us-central1-docker.pkg.dev </code>



<h2>Create GKE Autopilot cluster and configure secret to use Artifact registry</h2>

<<<<<<< HEAD

<code>gcloud container clusters create {Cluster_name}
</code>
=======
```gcloud container clusters create {Cluster_name}```

Create a secret to store artifact_registry as a registry which will later be used to pull the docker image.
>>>>>>> develop


<<<<<<< HEAD

<code>kubectl create secret docker-registry artifact-registry \
=======
kubectl create secret docker-registry artifact-registry \
>>>>>>> develop
--docker-server=https://{REGION}-docker.pkg.dev \
--docker-email={SA_ID}@{PROJECT_ID}.iam.gserviceaccount.com \
--docker-username=_json_key \
--docker-password="$(cat KEY-FILE)"


Open your default service account:


kubectl edit serviceaccount default --namespace default

Every namespace in your Kubernetes cluster has a default service account called default. This default service account is used to pull your container image.

Add the newly created imagePullSecret secret to your default service account:

```python
 imagePullSecrets:
 - name: artifact-registry
```
Your service account should now look like this:

<h3>Build and push the docker image to Artifact repository manually.</h3>

<<<<<<< HEAD

<code>docker build -t us-central1-docker.pkg.dev/production-api-enabill/artifact-k8s/django-k8s:latest .
</code>


<code>docker push us-central1-docker.pkg.dev/production-api-enabill/artifact-k8s/django-k8s --all-tags
</code>
=======

```docker build -t us-central1-docker.pkg.dev/production-api-enabill/artifact-k8s/django-k8s:latest .```

```docker push us-central1-docker.pkg.dev/production-api-enabill/artifact-k8s/django-k8s --all-tags```

>>>>>>> develop


<h2>Automate using GCP CloudBuild service</h2>


1. Clone the code from GCP repo
2. Build container image
3. Pushes the image to Artifact Registry

Run the following gcloud command to deploy

<<<<<<< HEAD

<code>gcloud build submit </code>

=======
>>>>>>> develop

```gcloud build submit ```