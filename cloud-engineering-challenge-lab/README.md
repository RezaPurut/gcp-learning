# Cloud Engineering: Challenge Lab
## Task 1: Create development VPC manually
Create a VPC called `griffin-dev-vpc` with the following subnets only:

`griffin-dev-wp`
* IP address block: 192.168.16.0/20

`griffin-dev-mgmt`
* IP address block: 192.168.32.0/20

1. Create the VPC
```
gcloud compute networks create griffin-dev-vpc --subnet-mode=custom
```
2. Create the subnets
```
gcloud compute networks subnets create griffin-dev-wp \
--network griffin-dev-vpc --region=us-east1 --range=192.168.16.0/20
```
```
gcloud compute networks subnets create griffin-dev-mgmt \
--network griffin-dev-vpc --region=us-east1 --range=192.168.32.0/20
```
3. Create firewall-rules to allow inbound ssh, icmp and rdp for `dev` and `prod` vpc (prod vpc already created during the initialization of this lab).
```
gcloud compute firewall-rules create griffin-dev-allow-icmp-ssh-rdp \
--direction=INGRESS \
--priority=1000 \
--network=griffin-dev-vpc \
--action=ALLOW \
--rules=icmp,tcp:22,tcp:3389 \
--source-ranges=0.0.0.0/0
```
```
gcloud compute firewall-rules create griffin-prod-allow-icmp-ssh-rdp \
--direction=INGRESS \
--priority=1000 \
--network=griffin-prod-vpc \
--action=ALLOW \
--rules=icmp,tcp:22,tcp:3389 \
--source-ranges=0.0.0.0/0
```
4. List network
```
gcloud compute networks list
```
5. List firewall-rules sorted by VPC network
```
gcloud compute firewall-rules list --sort-by=NETWORK
```
## Task 2: Create production VPC using Deployment Manager
1. Copy all files from `gs://cloud-training/gsp321/dm`
```
gsutil cp -r gs://cloud-training/gsp321/dm .
```
```
cd dm/
```
```
ls
```
2. Change zone in `prod-network.yaml` file to our zone: `us-east1-b`. Also check the subnet, make sure it is set to the subnet value that we created before
```
nano prod-network.yaml
```
3. Create the production VPC
```
gcloud deployment-manager deployments create griffin-prod-configuration --config prod-network.yaml
```
## Task 3: Create bastion host
1. Create instance. **Navigation menu** > **Compute Engine** > **VM instances**
```
zone: us-east1-b
network interface: griffin-dev-mgmt
network interface: griffin-prod-mgmt
```
