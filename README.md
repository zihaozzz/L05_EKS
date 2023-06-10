- Create S3 bucket `tfstate-willido` and dynamodb table `ekscluster-dev` for remote state

```
  backend "s3" {
    bucket = "tfstate-willido"
    key    = "eks-cluster/dev/terraform.tfstate"
    region = "ap-southeast-2" 
 
    # For State Locking
    dynamodb_table = "ekscluster-dev"    
  } 
```



**Go to `terraform` directory:**

- Update general environment in terraform.tf, will generate the cluster named ` tech-dev-eksdemo1`

```
aws_region = "ap-southeast-2"
environment = "dev"
business_divsion = "tech"
```

- Terraform create eks cluster resources

```
terraform init
terraform apply -auto-approve
```

- Configure kubeconfig for kubectl

```
export KUBECONFIG=~/.kube/eks-tech-dev-demo
aws eks --region ap-southeast-2 update-kubeconfig --name tech-dev-eksdemo1
//add ~/.kube/eks-tech-dev-demo to your .bashrc or .zshrc
```

- Show / switch contexts

```
k config current-context
k config get-contexts
k config use-context <context_name>
```

- List Worker Nodes

```
kubectl get nodes
kubectl get nodes -o wide
```

- Create namespace `jr-tut` and set as default ns

```
alias k=kubectl
k create ns jr-tut
k get ns
k config set-context --current --namespace jr-tut
```

- Create resources(po, svc, etc.) in imperative way

```
k run test-pod --image nginx
k expose pod test-pod -n jr-tut --type=LoadBalancer --port=80 --name=test-pod-svc
```

- Verify  resources

```
k get po
k get svc
k get po -o wide
k get po -o yaml
```

- Delete resrouces

```
k delete svc test-pod-svc
k delete po test-pod
```

- In root directory, create resources in declarative way

```
k apply -f ./manifests
```

- Check the deployment and service

```
k get deploy -o wide
k get rs -o wide
k get po -o wide
k get svc -o wide
//show po/deploy/rs/svc at once
k get all -o wide 
k describe deploy/app1-nginx-deployment
k describe svc/app1-nginx-clusterip-service
```

- scale up/down the deployment (or replicaset)

```
k scale deploy/app1-nginx-deployment  --replicas=3
k diff -f 01-NginxApp1-Deployment.yml
k edit deploy/app1-nginx-deployment //change the replica to 2
```

- View logs of pods

```
k logs -f app1-nginx-deployment-56959b9f6b-tgxcc //-f: follow
```

- Execute commands in/outside pods

```
k exec -it  app1-nginx-deployment-56959b9f6b-tgxcc -- /bin/bash
k exec -it  app1-nginx-deployment-56959b9f6b-tgxcc -- ls
k exec -it  app1-nginx-deployment-56959b9f6b-tgxcc -- env
```

- Delete resources

```
k delete -f ./manifests
k delete ns jr-tut
cd ./terraform && terraform destroy -auto-approve
```

