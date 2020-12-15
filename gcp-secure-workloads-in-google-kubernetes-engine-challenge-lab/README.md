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
