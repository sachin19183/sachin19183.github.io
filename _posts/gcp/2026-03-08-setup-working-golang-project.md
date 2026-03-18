---
layout: single
title: How to setup a golang client server project in GCP
date: 2026-03-08
categories: [GCP]
---
This post document my hands-on experiment in deloying a tcp server client program in GCP.

Step-1 Create a project folder in your local machine. write your code files in there.
create a docker file in the same folder. 
Step-2 Create a cloud storage bucket in GCP.
Step-3 upload your project folder in this cloud storage bucket
Step-4 For a simple project like this we will pick the google cloud build over google compute engine VM. Create cloud build configuration file.
cloudbuild.yaml
Step-5 copy the cloudbucket to your local folder, This should contain your code, the DockerFile and cloudbuild.yaml file
```bash
    gsutil cp -r gs://<cloud bucket path> .
```

Step-6 build the service using cloudbuild
```bash
     gcloud builds submit --config cloudbuild.yaml
```   
 This docker image is stored in Artifact Registry. You can see it via url- gcr.io/<project name>/<image name>
 
 Another way to deploy which worked finally is from your local machine go to the code folder. from there open gitbach or powershell and execute this command to build the image .
 ```bash
     gcloud builds submit --tag gcr.io/golang-handson-439117/golang-server .
 ```
 Till now we have used Google Cloud Build to build the docker images, Now to deply these images, we can either use GKE (Kubernetes Engine) or GCE (Compute Engine)
 
 Deploying the Docker image on VM using GCE:
 1) create VM instance. ssh to that instance
 2) Install docker on this vm
```bash
    sudo apt-get update
    sudo apt-get install -y docker.io
    sudo systemctl start docker
    sudo systemctl enable docker
```

4) now pull the docker image for server 
```bash
    gcloud auth configure-docker gcr.io
    docker pull \
      gcr.io/golang-handson-439117/golang-server:latest
```
  These commands can be copied from the docker image screen (three vertical dots to the right, click and the option is there.)
	I got two errors:
```bash  
	Error response from daemon: Head "https://gcr.io/v2/golang-handson-439117/golang-server/manifests/latest": denied: Unauthenticated request. Unauthenticated requests do not have permission "artifactregistry.repositories.downloadArtifacts" on resource "projects/golang-handson-439117/locations/us/repositories/gcr.io" (or it may not exist)
```
  First i verified that the image exist by going into the Artifact Registry-> gcr.io.
	Then: Enable Artifact Registry API
  To ensure that the API for accessing GCR is enabled, you can do the following:

## Go to Google Cloud Console.

Navigate to APIs & Services > Library.
Search for Artifact Registry API and ensure that it is enabled for your project.
Verify Service Account Permissions (If Step 2 Fails)
If you still face issues after running the authentication command, it’s possible that the service account associated with your GCE VM doesn’t have the necessary permissions to access the GCR repository. Follow these steps:

Go to the Google Cloud Console.
Navigate to IAM & Admin > IAM.
Find the service account that your GCE VM is using (likely something like compute@developer.gserviceaccount.com).
Ensure the service account has the following role: **Artifact Registry Reader (or the more general Storage Object Viewer).**
If it doesn’t, click on Edit (pencil icon) next to the service account and add the role

This was also not solving the problem.
I got the second error:
```bash
  docker pull \
  >     gcr.io/golang-handson-439117/golang-server:latest
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/images/create?fromImage=gcr.io%2Fgolang-handson-439117%2Fgolang-server&tag=latest": dial unix /var/run/docker.sock: connect: permission denied
```
The error you're seeing, permission denied while trying to connect to the Docker daemon socket, indicates that your current user does not have permission to interact with the Docker daemon. This is a common issue and can be resolved by either running Docker commands as root or adding your user to the docker group. 
```bash
    sudo usermod -aG docker $USER
    newgrp docker
```
Now we pull the docker image from artifact registry:
```bash
    gcloud auth configure-docker gcr.io
    docker pull gcr.io/golang-handson-439117/golang-server:latest
```
now it should work.

4) Run the server container using the pulled image
```bash
sudo docker run -d -p 8080:8080 gcr.io/golang-handson-439117/golang-server
```

---

The real issue was in Dockerfile where the base image for building was kept as the OS without installing golang in it. we solved it by making golang as the base image for building.
Forget building from windows laptop, its not working, second approach is we move our code in gcp. we copy the code folder from cloud storage to google cloudshell which is outside of vm till now.  .
```bash
    abc@cloudshell:~/project/server (golang-handson-439117)$ gsutil cp -rp gs://def-gcp-storage/GoCodeRepo/golang-server .
```

next step we build from google cloudshell.

build using this command:
```bash
    gcloud builds submit --tag gcr.io/golang-handson-439117/golang-server .
if you get the error:
unknown: Service 'containerregistry.googleapis.com' is not enabled for consumer 'project:your-project-id'.
fix it by running the command 
    gcloud services enable containerregistry.googleapis.com
```
then ssh to your vm.
```bash
    gcloud compute ssh golang-server --zone us-central1-f
```
Now we pull the docker image from artifact registry:
```bash
    gcloud auth configure-docker gcr.io
    docker pull gcr.io/golang-handson-439117/golang-server:latest
then we run the docker.
    sudo docker run -d -p 8080:8080 gcr.io/golang-handson-439117/golang-server
```
now server is running. to install the client.
we first follow the above process to build the client docker image. next step is to insatll it on a vm. for this we use the efficient way of first creating a machine image of the golang-server VM which has all the preconditions installed (docker is installed, all IAM permissions are there.)
Then using this machine image we create an instance template. and then using this instance template we create the VM
 golang-project-image
