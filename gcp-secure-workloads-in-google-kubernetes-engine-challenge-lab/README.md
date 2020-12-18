## Task 0: Download the necessary files
1. Open cloud shell and run the following command.
```
gsutil -m cp gs://cloud-training/gsp335/* .
```

## Task 1: Setup Cluster
```
gcloud container clusters create kraken-cluster1  \
--node-locations=us-central1-c \
--num-nodes=2 \
--machine-type=n1-standard-4 \
--enable-network-policy \
--zone=us-central1-c
```

## Task 2: Setup WordPress
1. Create a Cloud SQL instance in us-central1, using the default values
```
gcloud sql instances create kraken-sql --root-password=root
```
2. Create a Cloud SQL database for WordPress
```
gcloud sql databases create wordpress --instance=kraken-sql 
```
3. Create users using the following values
```
gcloud sql users create wordpress --instance=kraken-sql --password=wordpress --host=%

gcloud sql users create test --instance=kraken-sql --password=wordpress --host=%
```
### Create a service account for access to your WordPress database from your WordPress instances
4. Create the service account
```
gcloud iam service-accounts create kraken-sa  --display-name="kraken-sa"
```
5. Bind the service account to your project, give the role `roles/cloudsql.client`
```
MYPROJECT=$(gcloud config list --format 'value(core.project)')

gcloud projects add-iam-policy-binding "${MYPROJECT}" --role=roles/cloudsql.client --member="serviceAccount:kraken-sa@${MYPROJECT}.iam.gserviceaccount.com"
```
 6. Obtain the service acc credentials
 ```
 gcloud iam service-accounts keys create key.json --iam-account "kraken-sa@${MYPROJECT}.iam.gserviceaccount.com"
 ```
 7. Save the service account credentials in a json file
 ```
 gcloud auth activate-service-account --key-file=key.json
 ```
 *NOTE: In case you do this in one user/account, you will need to reactivate the it. Use this command:*
 `gcloud auth login`
 
 8. Save the service account json file as a secret in your Kubernetes cluster
 
 Below command before the `kubectl` is an additional step to allow kubectl to use these credentials when communicating with the cluster:
 
 ```
 gcloud container clusters get-credentials kraken-cluster1 --zone us-central1-c
 ```
 ```
 kubectl create secret generic cloudsql-instance-credentials --from-file key.json
 ```
