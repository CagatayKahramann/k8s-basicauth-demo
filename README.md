# k8s-basicauth-demo

## Introduction
This project demonstrates the deployment of a Next.js dashboard application using Kubernetes, with basic authentication enabled via Ingress. The deployment utilizes Minikube for local development and testing.

## Description
The Next.js dashboard application is containerized and deployed on a Kubernetes cluster. The deployment includes:
- A Deployment for managing the application pods.
- A Service for exposing the application within the cluster.
- An Ingress resource for external access, protected by basic authentication.

This setup is ideal for developers looking to understand how to deploy a web application on Kubernetes with secure access.

The dashboard used in this project is a default template from [NextAdminHQ/nextjs-admin-dashboard](https://github.com/NextAdminHQ/nextjs-admin-dashboard).

## Table of Contents
- [Introduction](#introduction)
- [Description](#description)
- [Table of Contents](#table-of-contents)
- [Prerequisites](#prerequisites)
- [Basic Configuration of Minikube](#basic-configuration-of-minikube)
- [Setup](#setup)
- [Deploying on Your Own Cluster](#deploying-on-your-own-cluster)
- [Troubleshooting](#troubleshooting)

## Prerequisites
Before you begin, ensure you have the following installed:

- **Minikube**: Minikube is a tool that runs a single-node Kubernetes cluster on your local machine. It is designed for users looking to try out Kubernetes or develop with it day-to-day. Minikube can be installed by following the instructions [here](https://minikube.sigs.k8s.io/docs/start/).

- **kubectl**: kubectl is the command line tool for interacting with Kubernetes clusters. It allows you to run commands against Kubernetes clusters, deploy applications, inspect and manage cluster resources, and view logs. Install kubectl by following the instructions [here](https://kubernetes.io/docs/tasks/tools/).

- **htpasswd**: htpasswd is a utility from the Apache HTTP Server project used to create and update files used to store usernames and password for basic authentication of HTTP users. You can install htpasswd as part of the Apache HTTP Server tools. On Ubuntu or Debian, you can install it using:
  ```bash
  sudo apt-get install apache2-utils
  ```
  On MacOS, you can install it using:
  ```bash
  brew install httpd
  ```
  
## Basic Configuration of Minikube

If you decide to use Minikube, follow these steps to set up your environment:

1. **Start Minikube**:
   ```bash
   minikube start
   ```
2. **Enable Metrics Server**:
   Enable the metrics server addon within Minikube:
   ```bash
   minikube addons enable ingress
   ```
   
## Setup

**Important Note**: During the setup process, it is crucial to apply the manifests in the correct order, one by one, to ensure proper configuration.

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/CagatayKahramann/k8s-basicauth-demo.git
   cd k8s-basicauth-demo
   ```

2. **Create the htpasswd File and Secret:**
   
   ```bash
   htpasswd -c auth <username>
   kubectl create secret generic basic-auth --from-file=auth
   ```
   Replace `<username>` with your desired username. This command will prompt you to set a password for the specified username.


3. **Apply the Deployment and Service:**
   
   ```bash
   kubectl apply -f nextjs-dashboard-deployment.yaml
   ```
   If you need to customize the deployment, such as changing the image or ports, modify the `nextjs-dashboard-deployment.yaml` file accordingly. For the default image the `targetPort: 3000` should be kept as it is because the app doesn't work correctly otherwise.

4. **Apply the Ingress:**
   
   ```bash
   kubectl apply -f nextjs-dashboard-ingress.yaml
   ```
   If you want to change the host, update the `nextjs-dashboard-ingress.yaml` file's host field to your desired domain.

5. **Update /etc/hosts:**
   Add the following line to your `/etc/hosts` file to access the application:
   ```bash
   <minikube-ip> nextjs-dashboard.local
   ```
   Replace `<minikube-ip>` with the IP address obtained from the `minikube ip` command.
   The default Ingress configuration provided uses the hostname `nextjs-dashboard.local`. If you need to use a different hostname, you can edit the Ingress manifest file and modify the host field in the `/etc/hosts` to specify a different hostname according to your needs.

6. **Access the Application:**
   Open your browser and navigate to `http://nextjs-dashboard.local`. You will be prompted for a username and password. Use the credentials created with `htpasswd` to login.

## Deploying on Your Own Cluster

f you choose to deploy this on your own Kubernetes cluster instead of Minikube, ensure that you have an Ingress controller installed on your cluster. The setup steps remain the same, but you may need to adjust the domain name in the Ingress configuration and update your DNS settings accordingly.

## Troubleshooting

## Troubleshooting

- **Minikube Issues**:
  - If Minikube fails to start, ensure you have virtualization enabled on your machine.
  - Check Minikube logs for errors: `minikube logs`.

- **Ingress Not Working**:
  - Ensure the Ingress addon is enabled: `minikube addons enable ingress`.
  - Verify the Ingress resource is correctly applied: `kubectl get ingress`.

- **Basic Authentication**:
  - If the authentication popup does not appear, check the Ingress annotations for typos.
  - Verify the secret was created correctly: `kubectl get secret basic-auth -o yaml`.

For more detailed information and troubleshooting, refer to the [Kubernetes documentation](https://kubernetes.io/docs/).
