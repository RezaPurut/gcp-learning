# Google Cloud Essentials: Challenge Lab

## Task 1: Create a project jumphost instance
Can be done in two ways:
1. Cloud Shell
```
gcloud compute instances create nucleus-jumphost \
    --machine-type f1-micro \
    --zone us-east1-b
```
2. Cloud Console
**Navigation Menu** > **Compute Engine** > **VM instance** > Click **Create**
Then, change the **region** to **us-east1** and **zone** to **us-east1-b**. Machine type set to **f1-micro**

## Task 2: Create a Kubernetes service cluster
1. Create a cluster (just use the default value) to host the service
```
gcloud container clusters create nucleus-cluster
```
2. Authenticate the cluster
```
gcloud container clusters get-credentials nucleus-cluster
```
3. Use the Docker container hello-app (`gcr.io/google-samples/hello-app:2.0`) as a place holder. Deploy the app
```
kubectl create deployment nucleus-server \
    --image=gcr.io/google-samples/hello-app:2.0
```
4. Expose the app on port 8080
```
kubectl expose deployment nucleus-server --type=LoadBalancer --port 8080
```
