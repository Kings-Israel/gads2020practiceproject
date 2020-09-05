# LAB: APP DEV: DEPLOYING THE APPLICATION INTO KUBERNETES ENGINE: NODE.JS

## Objectives

 - Create Dockerfiles to package up the Quiz application frontend and backend code for deployment.
 - Harness Cloud Build to produce Docker images.
 - Provision a Kubernetes Engine cluster to host the Quiz application.
 - Employ Kubernetes deployments to provision replicated Pods into Kubernetes Engine.
 - Leverage a Kubernetes service to provision a load balancer for the quiz frontend.

## STEPS

1. Clone git repository containing code

    `git clone https://github.com/GoogleCloudPlatform/training-data-analyst`

2. Create link as shortcut to working directory

    `ln -s ~/training-data-analyst/courses/developingapps/v1.2/nodejs/containerengine ~/containerengine`

3. Change to working directory

    `cd ~/containerengine/start`

4. Configure the application.

    `. prepare_environment.sh`

        # The script file:
        # - Creates a Google App Engine application.
        # - Exports environment variables GCLOUD_PROJECT and GCLOUD_BUCKET.
        # - Runs npm install.
        # - Creates entities in Google Cloud Datastore.
        # - Creates a Google Cloud Pub/Sub topic.
        # - Creates a Cloud Spanner Instance, Database, and Table.
        # - Prints out the Google Cloud Platform Project ID.


5. Create Kubernetes Cluster

    `gcloud beta container --project "qwiklabs-gcp-02-c37ba8ce722f" clusters create "quiz-cluster" --zone "us-central1-b" --no-enable-basic-auth --cluster-version "1.15.12-gke.2" --machine-type "e2-medium" --image-type "COS" --disk-type "pd-standard" --disk-size "100" --metadata disable-legacy-endpoints=true --scopes "https://www.googleapis.com/auth/cloud-platform" --num-nodes "3" --enable-stackdriver-kubernetes --enable-ip-alias --network "projects/qwiklabs-gcp-02-c37ba8ce722f/global/networks/default" --subnetwork "projects/qwiklabs-gcp-02-c37ba8ce722f/regions/us-central1/subnetworks/default" --default-max-pods-per-node "110" --no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing --enable-autoupgrade --enable-autorepair --max-surge-upgrade 1 --max-unavailable-upgrade 0 `

6. Connect to the created kubernetes cluster in cloud shell

    `gcloud container clusters get-credentials quiz-cluster --zone us-central1-b --project qwiklabs-gcp-02-c37ba8ce722f`

7. List pods that are in the cluster

    `kubectl get pods`

8. Create of a custom Docker image using Google's NodeJS App Engine image as the starting point(in cloud shell editor)

9. - Frontend dockerfile configuration

    `FROM gcr.io/google_appengine/nodejs`
    `RUN /usr/local/bin/install_node '>=0.12.7'`
    `COPY . /app/`
    `RUN npm install -g npm@6.11.3 --unsafe-perm || \`
    `  ((if [ -f npm-debug.log ]; then \`
    `      cat npm-debug.log; \`
    `   fi) && false)`
    `RUN npm update`
    `CMD npm start`

10. - Backend dockerfile configuration

    `FROM gcr.io/google_appengine/nodejs`
    `RUN /usr/local/bin/install_node '>=0.12.7'`
    `COPY . /app/`
    `RUN npm install -g npm@6.11.3 --unsafe-perm || \`
    `  ((if [ -f npm-debug.log ]; then \`
    `      cat npm-debug.log; \`
    `   fi) && false)`
    `RUN npm update`
    `CMD npm start`

11. Build docker images
    1. Frontend

        `gcloud builds submit -t gcr.io/$DEVSHELL_PROJECT_ID/quiz-frontend ./frontend/`

    2. Backend

        `gcloud builds submit -t gcr.io/$DEVSHELL_PROJECT_ID/quiz-backend ./backend/`

12. Deploy resources in kubernetes cluster from yaml template files

    1. Frontend yaml template provisions 3 replicas of the frontend docker image in kubernetes pods

        `kubectl create -f ./frontend-deployment.yaml`

    2. Backend yaml template provisions 2 replicas of the backend docker image in kubernetes pods

        `kubectl create -f ./backend-deployment.yaml`

13. A frontend service yaml template exposes a load balancer that sends requests from clients to all three frontend replicas

    `kubectl create -f ./frontend-service.yaml`

