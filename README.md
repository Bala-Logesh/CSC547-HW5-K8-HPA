Motivation for this project.

- To get to know how to do Horizontal Scaling of Containerized application using Kubernetes. This example provides a demonstration on Horizontal Pods Autoscaling(HPA) on a node application.

How to set up the environment and required tools?

1. Docker for Mac/Ubuntu/Windows.

    - This is used for creating a containerized application and pushing an image to DockerHub to access it in Kubernetes configuration. You can also use a locally built image. But it is always better to push DockerHub to have an ease of access anywhere whether locally or AWS.

    - for Mac, `install Docker for Mac`


2. Minikube

    - Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day. [https://github.com/kubernetes/minikube]

    - for Mac, `brew cask install minikube`


2. VirtualBox

    - Virtual Box is used by Minikube to run a virtual machine for cluster set up purpose.

    - for Mac, `brew cask install virtualbox`


3. Kubectl

    - Kubectl is the command-line interface that lets you interact with Kuberentes. Run brew install kubectl in your Terminal.

    - Minikube will automatically install it as a dependency.


Steps to run the sample HPA after installing required tools.


- Below steps are to setting up Minikube.

    - Run `minikube start`. This command will take time on first run.
    - Check Kubectl installation by running `kubectl version`
    - Enable metrics-server using `minikube addons enable metrics-server`
        - metrics-server is used for monitoring the pod resource. e.g CPU utilization, memory
        - check the metrics-server using `kubectl top node`, enabling can take few seconds, so be patient
    - If enabling `metrics-server` doesn't work try this step:
        - `git clone https://github.com/kubernetes-incubator/metrics-server.git`
        - `cd metrics-server`
        - `kubectl create -f deploy/1.8+/`
        - check the metrics-server using `kubectl top node`

        
- Clone this project
    - Make sure Docker is running
    - In the root of this project, run `docker build -t csc547-hpa-node .` to create a local docker image using the Dockerfile
    - Run the command `minikube image load csc547-hpa-node` to load the local docker image into minikube
    - Navigate the k8s folder, run 
        - `kubectl apply -f deployment.yml` to create the deployment
        - `kubectl apply -f service.yml` to create the load balancer service
        - `kubectl apply -f hpa.yml` to create the HPA
    - To verify the creations, run
        - `kubectl get deploy` to confirm the creation of the deployment
        - `kubectl get service` to confirm the creation of the load balancer service
        - `kubectl get hpa` to confirm the creation of the HPA
    - Open new terminal, run `minikube dashboard` to open Kubernetes dashboard. This will open the dashboard in the browser.
    - Run `minikube service csc547-hpa-node` to check the node-app pod(container) is working or not. If not running, it will start the node application and open it in the browser.
    - The load is generated using the Apache Benchmark tool.
    - Check HPA demo live:
        - Open three different terminal
        - Terminal 1 -> Run `kubectl get deploy --watch`
        - Terminal 2 -> Run `kubectl get hpa --watch`
        - Terminal 3 -> 
            - Run `ab -c 5 -n 1000 -t 100000 <Address_of_the_node_application>` to run with a timeout
            - Run `ab -c 5 -n 20000 <Address_of_the_node_application>` to run 20000 requests without timeout. 
        - You can see authoscalling of pods in Terminal 1.
    - Modifications can be done in the [hpa.yaml](./k8s/hpa.yml) file in `maxReplicas` and `averageUtilization` fields.





