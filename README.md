# Google Cloud Platform demo

Demo using Google Cloud Storage to host a static site! :octocat:

## Setup

For more, see the [Hosting a static website](https://cloud.google.com/storage/docs/hosting-static-website) tutorial.

### Pre-requisites
1. Sign up for Google Cloud Platform if you haven't already.
1. Install the [`gcloud`](https://cloud.google.com/sdk/gcloud) CLI. This should also install `gsutil`.

### Steps
* `gcloud auth login` and select the Google account you linked to GCP.
* `gcloud projects create PROJECT_ID` to create a new project with a given `PROJECT_ID`
* `gcloud config set project PROJECT_ID`
* `gcloud beta billing accounts list` to find your billing account ID
* `gcloud beta billing projects link PROJECT_ID --billing-account BILLING_ACCOUNT_ID`
* `gcloud iam service-accounts create SA_NAME`
* `gcloud iam service-accounts list` to retrieve the email of the service account just created.
* `gcloud iam service-accounts keys create key.json --iam-account=SA_EMAIL`
* `gcloud auth activate-service-account SA_EMAIL --key-file=key.json`
* `gcloud projects add-iam-policy-binding PROJECT_ID --member=serviceAccount:SA_EMAIL --role=roles/storage.objectAdmin`
* `gsutil mb -b on gs://BUCKET_NAME`

## Usage
* `gsutil cp ./index.html gs://BUCKET_NAME`
* `gsutil iam ch allUsers:objectViewer gs://BUCKET_NAME`
* Your file is available at https://storage.googleapis.com/BUCKET_NAME/index.html 🚀

## Automatic deploys using GitHub Actions (optional)

* `cat key.json | base64` and set it as a `GCP_SA_KEY` [secret](https://docs.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* Set `PROJECT_ID` to a secret called `GCP_PROJECT_ID`.
* Upon every push to `index.html` on the `main` branch, the `upload.yml` GitHub Actions workflow will upload `index.html` to `gs://BUCKET_NAME`.