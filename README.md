# ddruk_cicd_main

## GCP Local Setup
### Install Gcloud using Brew
```
brew install google-cloud-sdk
```

### Samples
```
Create a project in your GCP console ([https://console.cloud.google.com/welcome](https://console.cloud.google.com/welcome)) with the name : hackathon0-project
<WRITE_YOUR_PROJECTID> : hackathon0-project_id
<SERVICE_AC_DISPLAYNAME> : hackathon0-sa
```

### Login To GCloud and Other Setup ( One Time Setup )
```
gcloud components update
gcloud auth login
gcloud config set project <WRITE_YOUR_PROJECTID>
gcloud iam service-accounts create <SERVICE_AC_DISPLAYNAME> --display-name <SERVICE_AC_DISPLAYNAME>
```

### Enable the APis
```
gcloud services enable cloudresourcemanager.googleapis.com \
    artifactregistry.googleapis.com \
    iam.googleapis.com \
    run.googleapis.com \
    storage.googleapis.com \
    --project=<WRITE_YOUR_PROJECTID>
```

### Set the IAM roles ((Service includes CloudRun, GCS, Artifact Registry))
```
gcloud projects add-iam-policy-binding <WRITE_YOUR_PROJECTID> \
    --member=serviceAccount:<SERVICE_AC_DISPLAYNAME>@<WRITE_YOUR_PROJECTID>.iam.gserviceaccount.com \
    --role=roles/resourcemanager.projectIamAdmin \
    --role=roles/iam.serviceAccountUser \
    --role=roles/run.admin \
    --role=roles/artifactregistry.writer \
    --role=roles/artifactregistry.reader \
    --role=roles/artifactregistry.admin \
    --role=roles/storage.admin \
    --role=roles/storage.objectAdmin \
    --role=roles/storage.objectViewer \
    --role=roles/storage.objectCreator
```

### Create Bucket
```
gcloud storage buckets create gs://<WRITE_BUCKET_NAME> --location=US --uniform-bucket-level-access
```
where, sample WRITE_BUCKET_NAME can be  hackathon-bucket-0

### Create GAR Registry
```
gcloud artifacts repositories create <REPOSITORY_ID> \
    --project=<WRITE_YOUR_PROJECTID> \
    --location=us-central1 \
    --repository-format=docker \
    --description="Docker repository for Python server"
```

sample REPOSITORY_ID can be 'python-server'


### Creating key.json for Service Account
```
gcloud iam service-accounts keys create key.json  --iam-account=<SERVICE_AC_DISPLAYNAME>@<WRITE_YOUR_PROJECTID>.iam.gserviceaccount.com 
```
Copy the content of key.json into GitHub Secrets in your repository. The GitHub secret key should be GCP_SA_KEY.