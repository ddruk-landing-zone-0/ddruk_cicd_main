# ddruk_cicd_main

## GCP Local Setup
### Install Gcloud using Brew
```
brew install google-cloud-sdk
```

###  Create a GCP Project
1. Go to Google Cloud Console [https://console.cloud.google.com/welcome](https://console.cloud.google.com/welcome)
2. Create a project with the name: hackathon0-project
3. See the Project ID (e.g., hackathon0-project)

### Set the Environment
```
export GCP_PROJECT_ID="hackathon0-project" # Write it properly after seeing the dashboard
export SERVICE_AC_DISPLAYNAME="hackathon0-sa"
export BUCKET_NAME="hackathon-bucket-0"
export GAR_REPOSITORY_ID="python-server"
export GAR_LOCATION="us-central1"
```

### Login To GCloud and Other Setup ( One Time Setup )
```
gcloud components update
gcloud auth login
gcloud config set project $GCP_PROJECT_ID
gcloud iam service-accounts create $SERVICE_AC_DISPLAYNAME --display-name $SERVICE_AC_DISPLAYNAME
```

### Enable the APis
```
gcloud services enable cloudresourcemanager.googleapis.com \
    artifactregistry.googleapis.com \
    iam.googleapis.com \
    run.googleapis.com \
    storage.googleapis.com \
    --project=$GCP_PROJECT_ID
```

### Set the IAM roles ((Service includes CloudRun, GCS, Artifact Registry))
```
for role in resourcemanager.projectIamAdmin \
            iam.serviceAccountUser \
            run.admin \
            artifactregistry.writer \
            artifactregistry.reader \
            artifactregistry.admin \
            storage.admin \
            storage.objectAdmin \
            storage.objectViewer \
            storage.objectCreator; do
    gcloud projects add-iam-policy-binding $GCP_PROJECT_ID \
        --member=serviceAccount:$SERVICE_AC_DISPLAYNAME@$GCP_PROJECT_ID.iam.gserviceaccount.com \
        --role=roles/$role
done
```

### Create Bucket
```
gcloud storage buckets create gs://$BUCKET_NAME --location=US --uniform-bucket-level-access
```

### Create GAR Registry
```
gcloud artifacts repositories create $GAR_REPOSITORY_ID \
    --project=$GCP_PROJECT_ID \
    --location=$GAR_LOCATION \
    --repository-format=docker \
    --description="Docker repository for Python server"
```

### Creating key.json for Service Account
```
gcloud iam service-accounts keys create key.json \
    --iam-account=$SERVICE_AC_DISPLAYNAME@$GCP_PROJECT_ID.iam.gserviceaccount.com
```
Copy the content of key.json into GitHub Secrets in your repository. The GitHub secret key should be GCP_SA_KEY.