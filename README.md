# Fleetman: Vehicle Tracking Application
Fleetman is a real-time vehicle tracking application built with microservices architecture, designed to showcase containerized deployments using Docker and Kubernetes.
It simulates a logistics fleet management system where vehicles stream location data, and users can visualize fleet movements in real-time.

## Main Features
- Microservices-based architecture – services for position-simulator, position-tacker, API gateway, ActiveMQ (message broker) and web UI.
- Real-time tracking – created Real-time-tracking using position-simulator and position-tracker microservices. It could easily be extended for real-use.
- Scalable deployment – designed for Kubernetes, enabling horizontal scaling of microservices.
- Dockerized components – each service packaged as a lightweight Docker image.
- Service discovery & load balancing – handled natively by Kubernetes.

## Kubernetes Deployment
Fleetman is optimized to run on Kubernetes clusters (This repo only talks about minikube deployment).
- Start the minikube:
   `minikube start`
- Deploy the workloads, service, database one-by-one or deploy everything at one-go:
   `kubectl apply -f <path_to_components>`
- Start the minikube-tunnel by:
   `minikube service fleetman-webapp` <br>
Checkout the yaml set to know more about it: [ns-default](https://github.com/Mujib-Ahasan/fleetman-minikube/tree/main/ns-default)
- Do enable this addon: `minikube addons enable metrics-server`. </br> Wait few moment and see last minute average cpu and memory usage: `kubectl top pod`
## Testing on local machine
    git clone https://github.com/Mujib-Ahasan/fleetman-minikube.git
    cd fleetman-minikube/ns-default 
Then follow the [process](#Kubernetes%20Deployment) to bring the project up and live. </br>
**Note: These steps demands docker/VirtualBox and minikube already configured on your local machine.**
## About The Project
First Deployments were created and pods were attached to them. Then services need to be attached to the pods using labels and selector. Only for the `fleetman-webapp` service, we
set the type to `NodePort` and assigned a port `30080`. For testing purpose one can set `fleetman-queue service` to `NodePort` and set a port (> 30000). For tracking data persistency,
we have mongo-stack and to make it happen we have PersistentVolumeClaim - what to get, and PersistentVolume - how to get. One thing to note, pods have alreday IP assigned but they
are ephemeral in nature, it is not possible for them to hold any IP, thats why we use service and attach to them! This is highly for networking purpose.

## Fleetman Microservice Architecture
We have a frontend webapp, actually a microservice deployment serves the visualization for vehicles tracking. Second we have an api-gateway, the only component to talk to the backend, ask for 
data. Third we have a postion-simulator, one of the important component in this architecture that actaully simulates the position of the vehicles. Then fourth, we have position-tracker,
which does the some calculation behind the wall. To communicate between them we have ActiveMQ - Message Broker. At last we have mongodb to store the vehicle history data. </br>
</br>

The position-simulator will simulates the new position, just a file based system sending data to ActiveMQ microservice as (lat, lon). ActiveMQ sends the messaages to position-tracker.
The api-gateway call the api and collect the data and sends it to webapp frontend. Websocket is configured to send the data. The Position-tracker actually stores the data in memory, 
but incase if this dies may cause a brutal logical failure. So configuring database and mount the data path into local machine (here minikube) would be a better idea. I didnot get a chance to
meet the developer and talk about the insights, but the whole project and components are clear to me! And the rest is the section which I did in this project. 
    
## Ingress
In this repo, a directory ingress is there. Inside that ingress.yaml is used to set routing rules. 
Before that enable the addon: `minikube addons enable ingress`. Then apply the ingress.yaml file.
Check the namespace `ingress-nginx`. Edit /etc/hosts file and add your domain for local testing (for wondows hosts file path: \Windows\System32\drivers\etc\hosts). 
Go to your browser and type your domain, now should be able to see web page.
