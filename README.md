**EKS Infrastructure Deployment**
**FOR BITBUCKET PIPELINE DEPLOYMENT**

This repository contains all the CloudFormation templates and BitBucket Pipeline to build an EKS infrastructure

*Click the Settings in the left pane to view the AWS variables under Pipelines - Repository Variables*

---

## PreReqs

 1. Create AWS IAM user (eg. bitbucket-pipeline-iam) to allow CloudFormation deployment
 2. Update the Pipeline Repository variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)
 3. Create EC2 key pair as 'pem' file (eg. eg. eks-node-key-pair-{environment})and store credentials in 
    Parameter store  (eg. /{environment}/eks-node-key-pair)

---

## Steps

 1. Update Cloudformation templates as needed.
 2. In the Bitbucket pipeline yaml file change the IAM user and role properties in the "Update ConfigMap" section. Also change the account number on the "Associate EKS service account with ingress controller" section
 *move the account and IAM role/user to pipeline variables
 3. Once checked in, the pipeline will automatically trigger and deploy the environment. Make sure you use detailed comments about the changes that were made.
 4. Create access key and secret key in AWS console for the IAM user that gets created from the pipeline.
 5. Connect to EKS cluster (see below for more details)

---

## Connect to EKS Cluster
Make sure **Kubectl**, **AWSCLI**, **HELM3**, and **aws-iam-authenticator** are installed on your terminal.


Run aws configure and enter the access key and secret key for the IAM user created from the pipeline.


run command below to configure kubectl and replace cluster and role variables
```
    aws --region us-east-1 eks update-kubeconfig --name CLUSTERNAME
```

Run **kubectl get pods** to verify you are connected then run other commands as needed to troubleshoot or configure cluster.

## Install Kubernetes Dashboard (optional)

Deploy the K8S dashboard

```
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml
```


Generate Token file to log into the dashboard and save the token output

```
    kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}')
```

Start the kubectl proxy with the command below

```  
    kubectl proxy
```

Log into the dashboard from a browser with the URL below

```
    http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#!/login
```

Supply the token when prompted

## Pipeline setup

The steps below were used to setup the repository to use a pipeline to build the AWS Infrastructure

1. Create IAM user with programmatic access only in the AWS console with the appropriate permissions needed to create the resources. Do not overprovision with Administrator access.
2. In the IaC repository, click on Repository settings and then Respository Variables under the Pipeline section
3. Create the variables below for the IAM user created above
```
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```
4. Under the Pipeline section, click on Settings and Enable Pipelines if it isn't already. Keep in mind when you check code in the Pipeline will automatically run, so you may want to temporarily disable this when making multiple changes. 

## Clone a repository

Use these steps to clone from SourceTree, our client for using the repository command-line free. Cloning allows you to work on your files locally. If you don't yet have SourceTree, [download and install first](https://www.sourcetreeapp.com/). If you prefer to clone from the command line, see [Clone a repository](https://confluence.atlassian.com/x/4whODQ).

1. You’ll see the clone button under the **Source** heading. Click that button.
2. Now click **Check out in SourceTree**. You may need to create a SourceTree account or log in.
3. When you see the **Clone New** dialog in SourceTree, update the destination path and name if you’d like to and then click **Clone**.
4. Open the directory you just created to see your repository’s files.

Now that you're more familiar with your Bitbucket repository, go ahead and add a new file locally. You can [push your change back to Bitbucket with SourceTree](https://confluence.atlassian.com/x/iqyBMg), or you can [add, commit,](https://confluence.atlassian.com/x/8QhODQ) and [push from the command line](https://confluence.atlassian.com/x/NQ0zDQ).