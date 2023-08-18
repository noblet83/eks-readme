# eks-readme

# EKS-A Workshop

We will be deploying the [Google Demo app](https://github.com/GoogleCloudPlatform/microservices-demo) to our EKS clusters.

## Standard EKS deployment

### Dependencies
* [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
* [eksctl](https://github.com/eksctl-io/eksctl#installation)
* [Git] (https://git-scm.com/downloads)
* Credentials for access to AWS account

### Cluster Creation

Create a cluster (this will take 15-20 min...).  The PROJECT_NAME must be unique:
   ```bash
   eksctl create cluster --name <PROJECT_NAME> --version 1.27 --nodes=2 --node-type=t2.medium
   ```

## Cluster Creation Validation and Health

```bash
eksctl get cluster --name <CLUSTER_NAME>
```

```bash
eksctl get nodegroup --cluster <CLUSTER_NAME>
```
## Stacks used to create cluster

```bash
aws cloudformation list-stacks
```

```bash
aws cloudformation describe-stacks —-stack-name <STACK NAME>
```

## VPC and Subnets

```bash
aws eks describe-cluster —-region us-east-1 —-name <CLUSTER_NAME>
```
## Enable Logging
CloudWatch logging will not be enabled for the cluster.  We will enable logging review the logs.

```bash
eksctl utils update-cluster-logging —-enable-types=all —-region=us-east-1 —-cluster=<CLUSTER_NAME> —-approve
```

## Deploy Application

Dowload the application:
```bash
git clone https://github.com/GoogleCloudPlatform/microservices-demo.git
```

The download should create a microservices-demo directory with all of the code

## Create the application:
```bash
kubectl apply -f ./microservices-demo/release/kubernetes-manifests.yaml
```

Wait for the pods to be ready.
```bash
kubectl get pods
```

After a few minutes, you should see the Pods in a `Running` state:

```bash
   NAME                                     READY   STATUS    RESTARTS   AGE
   adservice-76bdd69666-ckc5j               1/1     Running   0          2m58s
   cartservice-66d497c6b7-dp5jr             1/1     Running   0          2m59s
   checkoutservice-666c784bd6-4jd22         1/1     Running   0          3m1s
   currencyservice-5d5d496984-4jmd7         1/1     Running   0          2m59s
   emailservice-667457d9d6-75jcq            1/1     Running   0          3m2s
   frontend-6b8d69b9fb-wjqdg                1/1     Running   0          3m1s
   loadgenerator-665b5cd444-gwqdq           1/1     Running   0          3m
   paymentservice-68596d6dd6-bf6bv          1/1     Running   0          3m
   productcatalogservice-557d474574-888kr   1/1     Running   0          3m
   recommendationservice-69c56b74d4-7z8r5   1/1     Running   0          3m1s
   redis-cart-5f59546cdd-5jnqf              1/1     Running   0          2m58s
   shippingservice-6ccc89f8fd-v686r         1/1     Running   0          2m58s
   ```

Access the web frontend in a browser using the frontend's external IP
```bash
   kubectl get service frontend-external | awk '{print $4}'
```

Visit `http://EXTERNAL_IP` in a web browser to access your instance of Online Boutique.

View a list of all the microservices running
```bash
kubectl get services
```
View the nodes that each microservice is running in

```bash
kubectl get pods -o wide
```
## (Optional) Logging - can view in the console

Amazon EKS control plane logging provides audit and diagnostic logs directly from the Amazon EKS control plane to CloudWatch Logs in your account.

The following cluster control plane log types are available. Each log type corresponds to a component of the Kubernetes control plane:
   - Kubernetes API server component logs:
        Your cluster's API server is the control plane component that exposes the Kubernetes API.
   - Audit:
        Audit logs provide a record of the individual users, administrators, or system components that have affected your cluster.
   - Authenticator:
        Authenticator logs are unique to Amazon EKS. These logs represent the control plane component that Amazon EKS uses for Kubernetes Role Based Access Control.
   - Control Manager:
        The controller manager manages the core control loops that are shipped with Kubernetes.
   - Scheduler:
        The scheduler component manages when and where to run pods in your cluster. 

### Checking Logs

Describe the log group for the cluster:
```bash
aws logs describe-log-groups --log-group-name-prefix /aws/eks/<CLUSTER_NAME>/cluster
```
Describe the log streams for the cluster:
```bash
aws logs describe-log-streams --log-group-name /aws/eks/<CLUSTER_NAME>/cluster
```
Get the log events in the log streams on the cluster:
```bash
aws logs get-log-events --log-group-name /aws/eks/<CLUSTER_NAME>/cluster --log-stream-name <LOG_STREAM_NAME>
```
## (Optional) Delete Cluster
   ```bash
   eksctl delete cluster --name <PROJECT_ID>
   ```
