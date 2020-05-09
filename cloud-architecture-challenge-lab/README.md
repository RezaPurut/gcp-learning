# Cloud Architecture: Challenge Lab
## Task 1: Create the production environment
From Question: The previous Cloud Architect had written the Deployment Manager configuration to build the network for kraken's production environment.

So, let's check the jumphost in **Deployment Manager**

**Navigation menu** > **Deployment Manager** > check status of the jumphost

Wait till the deployment finished. 

1. Then go to **Navigation menu** > **Compute Engine** > **VM instance**.
2. Click SSH button next to jumphost instance
```
cd /work/dm
```
3. Replace SET_REGION to us-east1
```
nano prod-network.yaml
```
4. Create the network using Deployment Manager
```
gcloud deployment-manager deployments create kraken-prod-vpc --config prod-network.yaml
```
5. Return to cloud shell
6. Check subnet name. Go to **Navigation Menu** > **Kubernetes Engine** > **Clusters** > **Create Cluster** > change Region to **us-east1** > **Networking**.
7. Create a two (2) node cluster called kraken-prod in the kraken-prod-vpc
```
gcloud container clusters create kraken-prod --network kraken-prod-vpc --subnetwork kraken-prod-subnet --num-nodes=2 --zone us-east1-b
```
8. SSH to jumphost again.
9. Use kubectl with the files in /work/k8s to create the frontend and backend deployments and services (which will expose the frontend service via a load balancer).
```
cd /work/k8s
```
```
kubectl create -f deployment-prod-backend.yaml
```
```
kubectl create -f deployment-prod-frontend.yaml
```
10. Check created pods
```
kubectl get pods
```
11. Create services
```
kubectl create -f service-prod-backend.yaml
```
```
kubectl create -f service-prod-frontend.yaml
```
12. Check created services
```
kubectl get services
```
## Task 2: Setup the Admin instance
1. Create an instance called kraken-admin. Go to **Navigation menu** > **Compute Engine** > **VM instance** > **Create instance**.
Name: **kraken-admin**

Region: **us-east1**

Zone: **us-east1-b**

Machine type: **n1-standard1**

**Networking** > **Network interfaces**

Network: **kraken-mgmt-subnet**

Then add one more network interface

Network: **kraken-prod-subnet**

2. After the instance created, copy ID from its detail page.
## Monitoring
From Question: Monitor kraken-admin and if CPU utilization is over 50% for more than a minute you need to send an email to yourself, as admin of the system
1. **Navigation menu** > **Monitoring**
2. **Alerting** > **Create Policy**
Name: **kraken-admin-monitoring**

Click **Add Condition**

Target: **GCE VM instance**

Metric: **CPU utilization**

Filter: **instance id**

Condition: **is above**

Threshold: **50**

For: **1 minute**

Click **Add**

**Notification setting** > **Add Notification Channel** > **email**

## Task 3: Verify the Spinnaker deployment
1. Go to cloud shell.
2. To access the Spinnaker console use Cloud Shell and `kubectl` to port forward the spin-deck pod from port **9000 to 8080** and then use Cloud Shell's web preview.
3. Three ways
**a.** 
```
gcloud container clusters get-credentials spinnaker-tutorial --zone us-east1-b --project <project-id>
```
```
export DECK_POD=$(kubectl get pods --namespace default -l "cluster=spin-deck" \
    -o jsonpath="{.items[0].metadata.name}")
```
```
kubectl port-forward --namespace default $DECK_POD 8080:9000 >> /dev/null &
```
**b.**
```
gcloud container clusters get-credentials spinnaker-tutorial --zone us-east1-b --project <project-id> \
 && kubectl port-forward $(kubectl get pod --selector="app=spin,cluster=spin-deck" --output jsonpath='{.items[0].metadata.name}') 8080:9000
```
**c.**
