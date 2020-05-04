# Kubernetes in Google Cloud: Challenge Lab
## Task 1: Create a Docker image and store the Dockerfile
1. Open Cloud Shell and run this command
```
source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh)
```
2. Clone the repository (the repo is in the project)
```
gcloud source repos clone valkyrie-app
```
3. Change directory
```
cd valkyrie-app
```
4. Create a Dockerfile with this info
```
nano Dockerfile
```
```
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
```
5. Create a Docker image called valkyrie-app with the tag v0.0.1
```
docker build -t valkyrie-app:v0.0.1 .
```
6. Run `step1.sh` to check the progress
```
./step1.sh
```
## Task 2: Test the created Docker image
1. Launch a container using the image valkyrie-app:v0.0.1. Map the host’s port 8080 to port 8080 on the container. Name it `kraken-app` and add `&` at the end to let it run in the background 
```
docker run -p 8080:8080 --name kraken-app valkyrie-app:v0.0.1 &
```
2. Run `step2.sh` to check the progress
```
./step2.sh
```
## Task 3: Push the Docker image in the Container Repository
1. Before push the image, re-tag the container to gcr.io/YOUR_PROJECT/valkyrie-app:v0.0.1
```
docker tag valkyrie-app:v0.0.1 gcr.io/YOUR_PROJECT/valkyrie-app:v0.0.1
```
2. Push the Docker image
```
docker push gcr.io/YOUR_PROJECT/valkyrie-app:v0.0.1
```
## Task 4: Create and expose a deployment in Kubernetes
1. Change IMAGE_HERE in `k8s/deployment.yaml` file to gcr.io/YOUR_PROJECT/valkyrie-app:v0.0.1
```
nano k8s/deployment.yaml
```
2. Get the Kubernetes credentials
```
gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d
```
3. Create the deployment and the service
```
kubectl create -f k8s/deployment.yaml
kubectl create -f k8s/service.yaml
```
## Task 5: Update the deployment with a new version of valkyrie-app
1. Increase the replicas from 1 to 3
```
kubectl scale deployment valkyrie-dev --replicas=3
```
2. Merge kurt-dev into master
```
git merge origin/kurt-dev
```
3. Build the new code as version v0.0.2 of valkyrie-app
```
docker build -t gcr.io/YOUR_PROJECT/valkyrie-app:v0.0.2
```
4. Push the updated image to the Container Repository
```
docker push gcr.io/YOUR_PROJECT/valkyrie-app:v0.0.2
```
5. Update the deployment with a new version of valkyrie-app
```
kubectl edit deployment valkyrie-dev
```
