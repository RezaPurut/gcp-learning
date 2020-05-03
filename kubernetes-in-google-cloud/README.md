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
