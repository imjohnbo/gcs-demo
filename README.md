# Google Cloud Platform demo

Demo using Google Cloud Storage to host a static site! :octocat:

Presented at [Greer Programmers Group](https://www.meetup.com/Greer-Programmers-Group/events/lhkdwrybclbnb/) at Aug 10, 2020 in conjunction with [these slides](https://docs.google.com/presentation/d/e/2PACX-1vSQ-M68oMLMabk6m22uTaCr72W066tJ9pC5MOdTMV-UCWCbVQrOJGyRDWviyLZBR7q1cYvgcxXT8upr/pub?start=false&loop=false&delayms=3000&slide=id.p).

## Setup

For more, see the [Hosting a static website](https://cloud.google.com/storage/docs/hosting-static-website) tutorial.

### Pre-requisites
1. Sign up for Google Cloud Platform if you haven't already.
1. Install the [`gcloud`](https://cloud.google.com/sdk/gcloud) CLI. This should also install `gsutil`.

### Setup steps

Log into the CLI and create a new project, linking it to your billing account:
* `gcloud auth login` and select the Google account you linked to GCP.
* `gcloud projects create PROJECT_ID` to create a new project with a given `PROJECT_ID`
* `gcloud config set project PROJECT_ID`
* `gcloud beta billing accounts list` to find your billing account ID
* `gcloud beta billing projects link PROJECT_ID --billing-account BILLING_ACCOUNT_ID`

Create an app-specific, rights-restricted service account:
* `gcloud iam service-accounts create SA_NAME`
* `gcloud iam service-accounts list` to retrieve the email of the service account just created.
* `gcloud iam service-accounts keys create key.json --iam-account=SA_EMAIL` to create a `JSON` keyfile you can authenticate with in the next step.
* `gcloud auth activate-service-account SA_EMAIL --key-file=key.json` to act as the service account in the following CLI commands.
* `gcloud projects add-iam-policy-binding PROJECT_ID --member=serviceAccount:SA_EMAIL --role=roles/storage.admin` to add a role to the service account and add it to the project
* `gsutil mb -b on gs://BUCKET_NAME` to create a new GCS bucket with `BUCKET_NAME`

## Usage
* `gsutil cp ./index.html gs://BUCKET_NAME`
* `gsutil iam ch allUsers:objectViewer gs://BUCKET_NAME`
* Your file is available at https://storage.googleapis.com/BUCKET_NAME/index.html 🚀. This one is available [here](https://storage.googleapis.com/imjohnbo-gcp-demo/index.html).

## Automatic deploys using GitHub Actions (optional)

* `cat key.json | base64` and set it as a `GCP_SA_KEY` [secret](https://docs.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* Set `PROJECT_ID` to a secret called `GCP_PROJECT_ID`.
* Upon every push to `index.html` on the `main` branch, the `upload.yml` GitHub Actions workflow will upload `index.html` to `gs://BUCKET_NAME`.
