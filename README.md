# DataGhost


`________          __             ________.__                    __ `  <br />
`\______ \ _____ _/  |______     /  _____/|  |__   ____  _______/  |_` <br />



### Overview
Autoscaling API service to plug and play texts and text comparison methods.

### Breakdown
The API portion of this project is the start of a platform where users can select from a number of similarity comparison methods to compare two texts. Will add an additional endpoint to accept requests from other APIs to compare batches of their own text.

The infrastructure is based in AWS EKS, with an autoscaling cluster that deploys my dockerized API and is provisioned in Terraform. App can be substituted with a different image.

API can be pulled from dockerhub with `docker pull dataghost/comptext`

### Note before running commands

You must have an IAM account to use. If your workstation's default IAM is the one you want to use, the generated outputs will automatically use your IAM. Otherwise you will have to change the IAM in ~/.kube/config to the one you want.<br />
You need to install kubectl in order to execute commands and check on your cluster.<br />
You need to have Docker installed in order to do any real work with the apps you will be deploying. If your API is not on Docker, you will have to specify where the location of your image/web app.<br />
You can replace the image I pulled for your own, as long as it is wrapped in nginx or some WSGI to serve up your app.

### Commands once structure and IAM account is set up

-- start up cluster --<br />
terraform refresh<br />
terraform plan<br />
terraform apply<br /><br />
// copy outputted server and certificate to corresponding sections in kubeconfig //<br /><br />
-- apply cluster config --<br />
terraform output config_map_aws_auth > config_map_aws_auth.yaml<br />
kubectl apply -f config_map_aws_auth.yaml<br /><br />
-- deploy your app -> choose a different image name here if you wish to deploy a different app --<br />
kubectl create deployment comptext --image=dataghost/comptext:latest<br />
kubectl expose deployment comptext --type=LoadBalancer --name=comptext --port=80<br /><br />
-- check on deployment --<br />
kubectl get svc --all-namespaces<br />
kubectl get pods --all-namespaces<br />
kubectl logs comptext-685c84fd7f-*****<br /><br />
-- delete deployment and bring down cluster --<br />
kubectl delete deployment comptext<br />
kubectl delete svc comptext<br />
terraform destroy
