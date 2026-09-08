# nodejs-kubernetes-mongodb
A cloud-native Node.js application deployed on AWS EC2 using Docker and Kubernetes, with MongoDB configured as a three-node Replica Set. The project demonstrates containerization, Kubernetes Deployment, ConfigMap, Service, and AWS Load Balancer integration for scalable application deployme
# Cloud Native Node.js Application with Kubernetes and MongoDB

## Project Overview

This project demonstrates the deployment of a cloud native Node.js application using Docker, Kubernetes, MongoDB Replica Set, and AWS EC2.

The Node.js application provides REST APIs to access user data stored in MongoDB. MongoDB is deployed as a three node Replica Set using Kubernetes StatefulSet. The Node.js application is containerized using Docker and deployed on Kubernetes with multiple replicas.

The application is exposed to the internet using a Kubernetes LoadBalancer Service integrated with an AWS Load Balancer.

## Architecture

```text
User
 |
 v
AWS Load Balancer
 |
 v
Kubernetes Service
 |
 v
Node.js Deployment
 |
 +-------------------+
 |        |          |
 v        v          v
Node Pod Node Pod Node Pod
 |
 v
MongoDB Service
 |
 +-------------------+-------------------+
 |                   |                   |
 v                   v                   v
MongoDB 0          MongoDB 1          MongoDB 2
PRIMARY            SECONDARY          SECONDARY
 |
 v
MongoDB Replica Set
```

## Technologies Used

* Node.js
* Express.js
* MongoDB
* MongoDB Replica Set
* Docker
* Docker Hub
* Kubernetes
* Kubernetes StatefulSet
* Kubernetes Deployment
* Kubernetes ConfigMap
* Kubernetes Service
* AWS EC2
* AWS Load Balancer
* GitHub

## Project Structure

```text
backend
|
├── Dockerfile
├── node-config.yml
├── node-deploy.yml
├── node-svc.yml
├── sts-svc.yml
├── sts.yml
├── package.json
├── package-lock.json
├── server.js
└── node_modules
```

## File Description

### server.js

Contains the Node.js and Express application.

The application provides the following endpoints:

```text
GET /
GET /users
```

The `/` endpoint checks whether the Node.js server is running.

The `/users` endpoint retrieves user data from MongoDB.

### Dockerfile

The Dockerfile is used to create a Docker image for the Node.js application.

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY server.js ./

EXPOSE 3000

CMD ["node", "server.js"]
```

### sts.yml

Defines the MongoDB StatefulSet.

It creates three MongoDB pods:

```text
mongodb-0
mongodb-1
mongodb-2
```

MongoDB is configured with Replica Set name:

```text
rs0
```

### sts-svc.yml

Creates a Kubernetes Headless Service for MongoDB.

The Headless Service allows MongoDB pods to communicate using stable DNS names such as:

```text
mongodb-0.mongodb
mongodb-1.mongodb
mongodb-2.mongodb
```

### node-config.yml

Stores the MongoDB connection string and Node.js port as Kubernetes configuration.

Example:

```text
MONGO_URI=mongodb://mongodb-0.mongodb:27017,mongodb-1.mongodb:27017,mongodb-2.mongodb:27017/collage?replicaSet=rs0

PORT=3000
```

### node-deploy.yml

Creates the Node.js Kubernetes Deployment.

The deployment runs multiple Node.js application replicas for scalability and availability.

### node-svc.yml

Creates a Kubernetes LoadBalancer Service.

The service exposes the Node.js application outside the Kubernetes cluster through an AWS Load Balancer.

## MongoDB Replica Set

MongoDB is deployed using Kubernetes StatefulSet with three replicas.

The Replica Set contains:

```text
mongodb-0.mongodb:27017
mongodb-1.mongodb:27017
mongodb-2.mongodb:27017
```

The MongoDB Replica Set configuration is:

```text
Replica Set Name: rs0
```

The typical Replica Set state is:

```text
mongodb-0    PRIMARY
mongodb-1    SECONDARY
mongodb-2    SECONDARY
```

The PRIMARY node handles write operations while SECONDARY nodes replicate the data.

## Docker

The Node.js application is containerized using Docker.

Docker image:

```text
aarushtechnol/online-node:v1
```

The image is stored on Docker Hub and used by the Kubernetes Deployment.

## Kubernetes Deployment

The Node.js application is deployed using Kubernetes Deployment.

The deployment configuration uses:

```text
Replicas: 3
Container Port: 3000
```

Running multiple replicas improves application availability and allows Kubernetes to distribute requests between application pods.

## Kubernetes Service

The Node.js application is exposed using a Kubernetes LoadBalancer Service.

```text
Service Port: 80
Target Port: 3000
Service Type: LoadBalancer
```

The AWS Load Balancer provides external access to the application.

## API Endpoints

### Health Check

```text
GET /
```

Response:

```json
{
  "message": "Node.js server is running"
}
```

### Get Users

```text
GET /users
```

Returns user documents stored in MongoDB.

## Deployment Flow

```text
Node.js Application
        |
        v
Dockerfile
        |
        v
Docker Image
        |
        v
Docker Hub
        |
        v
Kubernetes Deployment
        |
        v
Kubernetes Service
        |
        v
AWS Load Balancer
        |
        v
Internet
```

## MongoDB Deployment Flow

```text
Kubernetes StatefulSet
        |
        +----------------+
        |        |       |
        v        v       v
   MongoDB 0 MongoDB 1 MongoDB 2
        |        |       |
        +--------+-------+
                 |
                 v
          MongoDB Replica Set
                 |
                 v
                rs0
```

## How to Deploy

### 1. Start the MongoDB StatefulSet

```bash
kubectl apply -f sts.yml
```

### 2. Create MongoDB Service

```bash
kubectl apply -f sts-svc.yml
```

### 3. Check MongoDB Pods

```bash
kubectl get pods
```

### 4. Initialize MongoDB Replica Set

Connect to the MongoDB primary pod and initialize Replica Set `rs0`.

### 5. Build Docker Image

```bash
docker build -t aarushtechnologies/online-node:v1 .
```

### 6. Push Image to Docker Hub

```bash
docker push aarushtechnologies/online-node:v1
```

### 7. Create Node.js ConfigMap

```bash
kubectl apply -f node-config.yml
```

### 8. Deploy Node.js Application

```bash
kubectl apply -f node-deploy.yml
```

### 9. Create Node.js Service

```bash
kubectl apply -f node-svc.yml
```

### 10. Check Deployment

```bash
kubectl get pods
kubectl get svc
```

## Application Testing

After the LoadBalancer gets an external address, open:

```text
http://LOAD_BALANCER_URL/
```

For users:

```text
http://LOAD_BALANCER_URL/users
```

## Security

Sensitive environment files such as `.env` are excluded from GitHub using `.gitignore`.

The project does not store database credentials or other sensitive configuration directly in the Git repository.

## Scalability

The project demonstrates horizontal scalability using Kubernetes.

The Node.js Deployment runs multiple application replicas:

```text
Node Pod 1
Node Pod 2
Node Pod 3
```

If one application pod fails, Kubernetes can maintain the desired number of replicas.

MongoDB uses a three node Replica Set to provide data replication and high availability.

## Key Features

* Cloud native Node.js application
* REST API
* Docker containerization
* Docker Hub image management
* Kubernetes Deployment
* Kubernetes StatefulSet
* Kubernetes ConfigMap
* Kubernetes LoadBalancer Service
* Three node MongoDB Replica Set
* AWS EC2 deployment
* AWS Load Balancer integration
* Horizontal application scaling
* GitHub version control

## Learning Outcomes

This project provides practical experience with:

* Node.js application development
* REST API development
* Docker containerization
* Docker image management
* Kubernetes deployments
* Kubernetes StatefulSets
* Kubernetes networking
* Kubernetes ConfigMaps
* MongoDB Replica Sets
* AWS EC2
* Load Balancer integration
* Git and GitHub

## Author

Ritesh Bansode

B.Tech Computer Science Engineering
