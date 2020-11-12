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
5. Get the external IP and go to the address with port 8080 to check whether it is working or not
```
kubectl get service
```

## Task 3: Setup an HTTP load balancer
1. Create ``startup.sh`` file. This file will be used to automate nginx installation
```
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```
2. Create an ```instance template``` to be used to create ```instance group``` later
```
gcloud compute instance-templates create nucleus-template \
    --metadata-from-file startup-script=startup.sh
```
3. Create a ```target pool``` that also will be used to create ```instance group```
```
gcloud compute target-pools create nucleus-pool
```
4. Create a managed ```instance group```. Try with 2 VM for this lab (can try more but worried that your account might be blocked). ```instance group``` allow us to manage multiple VM as a single entity.
```
gcloud compute instance-groups managed create nucleus-group \
    --base-instance-name nucleus \
    --size 2 \
    --template nucleus-template \
    --target-pool nucleus-pool
```
5. List instances
```
gcloud compute instances list
```
6. Create firewall rules to allow htt 80 traffic
```
gcloud compute firewall-rules create www-firewall --allow tcp:80
```
7. Create a health check
```
gcloud compute http-health-checks create http-basic-check
```
8. Define an HTTP service and map a port name to the relevant port for the instance group
```
gcloud compute instance-groups managed \
    set-named-ports nucleus-group \
    --named-ports http:80
```
9. Create a backend service
```
gcloud compute backend-services create nucleus-backend \
    --protocol HTTP --http-health-checks http-basic-check --global
```
10. Attach the managed instance group
```
gcloud compute backend-services add-backend nucleus-backend \
    --instance-group nucleus-group \
    --instance-group-zone us-east1-b \
    --global
```
11. Create a URL map
```
gcloud compute url-maps create web-map --default-service nucleus-backend
```
12. Create a Http proxy that will intercept the request and call the url map created before
```
gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map
```
13. Create a forwarding rule
```
gcloud compute forwarding-rules create http-content-rule \
    --global \
    --target-http-proxy http-lb-proxy \
    --ports 80
```
