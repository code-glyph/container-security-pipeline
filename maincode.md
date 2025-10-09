Here we will walk through a working Binary Authorization pipeline on Google Cloud, this has been implemented on Windows PowerShell. It covers: 

> Building and pushing an image to Artifact Registry
> Creating a KMS signing key
> Creating a Grafeas Note and Attestor
> Creating an Attestation for a specific image digest
> Enforcing a Binary Authorization policy
> Spinning up GKE and proving allow/deny behavior
> Cleaning up resources safely

Prerequisities 

> Google Cloud SDK installed and authenticated (gcloud auth login)
> PowerShell (Windows)
> Billing enabled (only needed while creating/deleting GKE & Artifact Registry)

# Set environment variables
$PROJECT_ID = "container-sec-binauth"
$PROJECT_NUMBER = "718313479012"
$REGION = "us-central1"
$ZONE = "us-central1-a"

$REPO = "app-repo"
$IMAGE = "hello"
$TAG = "1.0"
$AR_IMAGE = "$REGION-docker.pkg.dev/$PROJECT_ID/$REPO/$IMAGE:$TAG"

# Align ADC quota project
gcloud auth application-default set-quota-project $PROJECT_ID

# Optional: unique suffix for resource names
$RANDOM_SUFFIX = -join ((65..90) + (97..122) + (48..57) | Get-Random -Count 6 | % {[char]$_})

**#Configuring gcloud**
gcloud config set project "${PROJECT_ID}"
gcloud config set compute/region "${REGION}"

# Enable APIs
$services = @(
  "artifactregistry.googleapis.com",
  "container.googleapis.com",
  "binaryauthorization.googleapis.com",
  "containeranalysis.googleapis.com",
  "cloudkms.googleapis.com",
  "cloudbuild.googleapis.com"
)
$services | ForEach-Object { gcloud services enable $_ }
