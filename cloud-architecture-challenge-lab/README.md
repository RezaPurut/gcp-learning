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

