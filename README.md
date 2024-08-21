# microservice-demo-gcp
Microservices-Demo

Pre-requisites:

-	GCP account
-	CLI with gcloud, git, kubectl,helm

1.	Clone the repository

git clone https://github.com/Yj8055/microservice-demo-gcp.git
cd microservice-demo-gcp


2.	Export required variables

export PROJECT_ID={project_id}
export REGION={region}
export REPO_PREFIX=gcr.io/${PROJECT_ID}
export PROJECT_ID=${PROJECT_ID}
export TAG={tag}

Update varaibles according to your account

3.	Login to your GCP account

gcloud auth login


4.	enable container service for GCP

gcloud services enable container.googleapis.com \
  --project=${PROJECT_ID}

5.	Create GKE cluster

gcloud container clusters create-auto online-boutique \
  --project=${PROJECT_ID} --region=${REGION}

6.	Get credentials for GKE cluster

gcloud container clusters get-credentials online-boutique\
    --region=us-central1 --project=${PROJECT_ID}

7.	Run script to build required images and store in the Google container repository

./image-script/buildimages.sh


8.	Install ngrok 

helm repo add ngrok https://ngrok.github.io/kubernetes-ingress-controller
export NGROK_AUTHTOKEN={auth_token}
helm install ngrok-ingress-controller ngrok/kubernetes-ingress-controller \
	--set credentials.apiKey=$NGROK_API_KEY \
	--set credentials.authtoken=$NGROK_AUTHTOKEN

kubectl get pods -n ngrok-ingress-controller

export NGROK_DOMAIN="{domain}"

9.	create ingress for ngrok



10.	Access application on ngrok domain


Description:

GCP is used to create kubernetes cluster.
Helm-charts are used to apply kubernetes configurations.
Dockerfile is used to create application images, and Gcloud build submit is used to build and push image to container registry.
GCP artifact registry used to store application docker images, and same is referenced in helm chart values.
After creating application service, ngrok is used to expose URL through secure tunnel. https://moth-valued-bird.ngrok-free.app/

Best practices implemented:

Dockerfiles:
- Used multi-stage builds to minimize the number of layers and reduce image size.
- Used specific versions of base images to avoid unexpected issues due to changes in latest

Helm charts:
- Ensured the Helm chart directory structure follows the standard format: templates/, charts/, values.yaml, Chart.yaml, etc.
- Externalized configuration using values.yaml for flexibility and reusability.
- Followed semantic versioning in Chart.yaml and update the version for every change.

Issue faced and resolution:
1. GKE cluster not getting created.
  - It was happening due to unverified account, and wrong credentials. After verifying credntials, kubernetes API was not available.
    Once required API is installed, GKE cluster created successfully.
2. Pods are not getting created/ status showing crashloopback or imagepullback
  - checked the pod logs, and events to check what is issue behind crashloopback. For imagepullback issue, Checked the registry where images stored and verified value mentioned in yaml. From dockerHUB it was not working, so used google artifact registry to store images.
3. NGROK installation and service exposing 
  - NGROK installation involves complex steps for installing over kubernetes, and required to create seperate ingress which attaches to application service. 
    While creation face multiple issue, by following steps over documentation https://dashboard.ngrok.com/get-started/setup/kubernetes it was creted succesfully.
