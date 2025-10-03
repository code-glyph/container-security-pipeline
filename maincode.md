#Set Environment Variables
export PROJECT_ID="container-sec-binauth"
export PROJECT_NUMBER="718313479012"
export REGION="us-central1"
export ZONE="us-central1-a"

export REPO="app-repo"
export IMAGE="hello"
export TAG="1.0"
export AR_IMAGE="${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPO}/${IMAGE}:${TAG}"

#if you want you can generate unique suffix for resource names
RANDOM_SUFFIX=$(openssl rand -hex 3)

#Configuring gcloud
gcloud config set project "${PROJECT_ID}"
gcloud config set compute/region "${REGION}"

#enabling APIs
gcloud services enable \
  artifactregistry.googleapis.com \
  container.googleapis.com \
  binaryauthorization.googleapis.com \
  containeranalysis.googleapis.com \
  cloudkms.googleapis.com \
  cloudbuild.googleapis.com
