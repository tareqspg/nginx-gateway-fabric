[![FOSSA Status](https://app.fossa.com/api/projects/custom%2B5618%2Fgithub.com%2Fnginxinc%2Fnginx-gateway-kubernetes.svg?type=shield)](https://app.fossa.com/projects/custom%2B5618%2Fgithub.com%2Fnginxinc%2Fnginx-gateway-kubernetes?ref=badge_shield)

# NGINX Kubernetes Gateway

NGINX Kubernetes Gateway is an open source project that provides an implementation of the Kubernetes [Gateway API](https://gateway-api.sigs.k8s.io/) using NGINX as the data plane. 
The goal of this project is to implement the core Gateway APIs -- `Gateway`, `GatewayClass`, `HTTPRoute`, `TCPRoute`, `TLSRoute` and `UDPRoute` -- in order to configure an HTTP Load Balancer for applications running on Kubernetes.
NGINX Kubernetes Gateway is currently under development and supports a subset of the Gateway API.

> Warning: This project is actively in development (pre-alpha feature state) and should not be deployed in a production environment.
> All APIs, SDKs, designs, and packages are subject to change.

# Run NGINX Kubernetes Gateway

## Prerequisites

Before you can build and run the NGINX Kubernetes Gateway, make sure you have the following software installed on your machine:
- [git](https://git-scm.com/)
- [GNU Make](https://www.gnu.org/software/software.html)
- [Docker](https://www.docker.com/) v18.09+
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

## Build the image

1. Clone the repo and change into the `nginx-gateway-kubernetes` directory:

   ```
   git clone https://github.com/nginxinc/nginx-gateway-kubernetes.git
   cd nginx-gateway-kubernetes
   ```

1. Build the image:
 
   ```
   make PREFIX=myregistry.example.com/nginx-gateway container
   ```

   Set the `PREFIX` variable to the name of the registry you'd like to push the image to. By default, the image will be named `nginx-gateway:0.0.1`.


1. Push the image to your container registry:

   ```
   docker push myregistry.example.com/nginx-gateway:0.0.1
   ```

   Make sure to substitute `myregistry.example.com/nginx-gateway` with your private registry.

## Deploy NGINX Kubernetes Gateway

You can deploy NGINX Kubernetes Gateway on an existing Kubernetes 1.16+ cluster. The following instructions walk through the steps for deploying on a [kind](https://kind.sigs.k8s.io/) cluster. 

1. Load the NGINX Kubernetes Gateway image onto your kind cluster:

   ```
   kind load docker-image nginx-gateway:0.0.1
   ```

   Make sure to substitute the image name with the name of the image you built.


1. Install the Gateway CRDs:

   ```
   kubectl apply -k "github.com/kubernetes-sigs/gateway-api/config/crd?ref=v0.4.2" 
   ```
   
1. Install the NGINX Kubernetes Gateway CRDs:

   ```
   kubectl apply -f deploy/manifests/crds
   ```

1. Deploy the NGINX Kubernetes Gateway:

   Before deploying, make sure to update the Deployment spec in `nginx-gateway.yaml` to reference the image you built.

   ```
   kubectl apply -f deploy/manifests/nginx-gateway.yaml
   ``` 

1. Confirm the NGINX Kubernetes Gateway is running in `nginx-gateway` namespace:

   ```
   kubectl get pods -n nginx-gateway
   NAME                             READY   STATUS    RESTARTS   AGE
   nginx-gateway-5d4f4c7db7-xk2kq   2/2     Running   0          112s
   ```

# Test NGINX Kubernetes Gateway

To test the NGINX Kubernetes Gateway run:

```
make unit-test
```
