# Assignment

The goal of this particular case study was to build an infrastructure for Notejam application. Here I have chosen Flask framework for the implementation of the application.

This README.md file will explain the architecture of the infrastructure and the implementation. Also this documentation will explain in a way that this setup can be reproduced.

## Environment

This complete setup has been done on a macOS machine. 

	* Operating system: macOS Big Sur version 11.2.3
    * Docker-CE: version 20.10.5
    * minikube: version v1.18.1 
    * python3: version 3.8.1

## Implementation

The first step of this setup is to build the docker container for the application.

The complete folder structure will look like below

```
 ma.rahman@lenpc0wj7xt $ ~/Desktop/nordcloud-task/sample/flask $ tree
.
├── Dockerfile
├── README.rst
├── db.py
├── notejam
│   ├── __init__.py
│   ├── config.py
│   ├── forms.py
│   ├── models.py
│   ├── static
│   │   └── css
│   │       ├── style.css
│   │       └── tables.css
│   ├── templates
│   │   ├── _helpers.html
│   │   ├── base.html
│   │   ├── notes
│   │   │   ├── create.html
│   │   │   ├── delete.html
│   │   │   ├── edit.html
│   │   │   ├── form.html
│   │   │   ├── list.html
│   │   │   └── view.html
│   │   ├── pads
│   │   │   ├── create.html
│   │   │   ├── delete.html
│   │   │   ├── edit.html
│   │   │   ├── form.html
│   │   │   └── note_list.html
│   │   ├── user.html
│   │   └── users
│   │       ├── forgot_password.html
│   │       ├── settings.html
│   │       ├── signin.html
│   │       └── signup.html
│   ├── views.py
│   └── wsgi.py
├── notejam-deployment.yaml
├── notejam-pv.yaml
├── notejam-pvc.yaml
├── requirements.txt
├── run.sh
├── runserver.py
└── tests.py

```
### Notejam application dockerization

Clone

Clone the repo:

```
$ git clone https://github.com/Mahnaz15/Assignment.git YOUR_PROJECT_DIR/ 

``` 

All the necessary files to build the docker container using flask framework has been placed under ```flask``` folder. Change the directory to execute the following stages.

```
$ cd YOUR_PROJECT_DIR/flask/

```
The necessary binaries I got once I downloaded the resources for this challange. The ```Dockerfile``` is used to build the container.

The ```Dockerfile``` will look like below

```
FROM python:3.8-alpine
WORKDIR /app
COPY requirements.txt /app
RUN pip install -r requirements.txt
COPY db.py runserver.py /app
ADD notejam /app/notejam
RUN python db.py
EXPOSE 5000
ENTRYPOINT FLASK_APP=runserver.py flask run --host=0.0.0.0

```
As the application is written on python, so python:3.8-alpine has been used as base image.

To build the image

```
docker build  -t notejam .

```
After building the image it is visible in local machine

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
notejam                      latest              88b0acf68fda        2 weeks ago         90.4MB

```
Here the application will be exposed on port 5000. To deploy the container run ```docker run --name notejam-container -d  -p 5000:5000 notejam ``` and to see the running container on your local machine use ```docker ps -a```

To check the application status we can go to the browser and hit ```http://localhost:5000/``` and see the application running.

### Deploy Pod to Kubernetes

Once the application is containerized, now we can deploy the application on kubernetes cluster. Here I have used minikube to deploy the application

To install minikube on local machine, follow the documentation from the official site: [minikube](https://minikube.sigs.k8s.io/docs/start/)

I have pushed the image to my private registry using the command ```docker push mahnaz15/demo-app:notejam-1.0```

After pushing the image to the registry, I have created a deployment configuration file to deploy the pods into kubernetes cluster. Also to persist the user credentials and notes I have created a persistent volume and persistent volume claim.

The whole setup for deploying notejam application into the kubernetes cluster contains following files

```
.
├── notejam-deployment.yaml
├── notejam-pv.yaml
├── notejam-pvc.yaml

```
To deploy the pods to kubernetes, the following commands are used:

```
$ kubectl apply -f notejam-deployment.yaml
$ kubectl apply -f notejam-pv.yaml
$ kubectl apply -f notejam-pvc.yaml

```
Run ```kubectl get deployments``` to check if the Deployment was created. The output will be similar to this

```
NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
notejam-deployment   1/1     1            1           11d
```
to check the pods run ```kubectl get pods``` and to check the ```pv``` and ```pvc```  run ```kubectl get pv``` and ```kubectl get pvc```

To verify the application running on cluster, use port forwarding to access application in a cluster with the following command.

```kubectl port-forward pod/notejam-deployment-6bc6fc6cc5-qbthw 5000:5000 ``` 

Change here notejam-deployment-6bc6fc6cc5-qbthw to the name of the pod.

One of the requirments of this assignment is that the application must serve variable amount of traffic. To meet this requirement, we can use Horizontal Pod Autoscaler which can scale the number of pods in a deployment based on observed CPU utilization. 

To check the current status of autoscaler by running:

```
kubectl get hpa

NAME                 REFERENCE                       TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
notejam-deployment   Deployment/notejam-deployment   0%/50%    1         5         1          2m58s

```

## Further Implementation

To design the current architecture on a public cloud platform, I have planned to build this on AWS cloud platform.

* Creating a kubernetes cluster, we will use EKS (Amazon Elastic Kubernetes Service) cluster. To build simply the cluster, we will use Terraform.
* There is another requirement in this challange, is to preserve the notes upto 3 years and recover it when needed. In this scenario, we can use EBS (Elastic Block Store) volume. This will allow to persist data even after termination of the instances. To preserve the notes for a time period we can use volume snapshot by defining the policy. 
* In case of datacenter failures, we will deploy multiple cluster.
* To work on different environments, we will deploy different cluster. For instance, development cluster, Test cluster and Production cluster.
* Finally, to monitor the application and to see the relevant metrics, we will implement prometheus and grafana. For logging, we will use EFK stack. Elasticsearch will be used to store log data. Before storing data, fluentd will collect and process the data. Then it will send to Elasticsearch to store. Also, to visualize the data in a user interface, we will use kibana.  
