# bigopencloud/Kubernetes

KOPS is an effort to automate the instalation and configuration of Kubernetes Cluster Environment.
Currentlly it fully supported on AWS.

I think it is is not yet supported on Azure platform,it might be bacuse Azure has its own Resoucre Manager scripts to install and configure Kubernetes on Azure platform.I have tried it on Azure platform and based on my own learning i am giving a step by step procedure to install,configure and use Kubernetes Cluster on AWS cloud environment.

This Quickstart script is Based on the Tutorial on https://github.com/kubernetes/kops/blob/master/docs/aws.md

It is better to have a fresh Virtual machine on Virtual Box or VMware Workstation.I am using a minimal CentOS 7 instalation.

1.Install the basic tools for your CentOS 7.

sudo yum install update -y
sudo yum install net-tools -y
sudo yum install wget -y

2.Download and install kubectl

$curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
$chmod +x ./kubectl
$sudo mv kubectl /usr/local/bin/kubectl

3.Download and install kops

$wget https://github.com/kubernetes/kops/releases/download/1.7.0/kops-linux-amd64
$chmod +x kops-linux-amd64  
$mv kops-linux-amd64 /usr/local/bin/kops 

4.Setup your AWS CLI environment

4.1.Install python 3.6

$sudo yum -y install https://centos7.iuscommunity.org/ius-release.rpm
sudo yum -y install python36u
$sudo yum -y install python36u-pip
$pip3.6 install awscli --upgrade --user

4.2 Configure AWS CLI

$aws configure

$ aws configure

AWS Access Key ID [None]: XXXXXAKIAI2TVKXXX

AWS Secret Access Key [None]: KhXXXXXXXXX8Li7FZwP42OviGXXX

Default region name [None]: us-east-2

Default output format [None]: json

5.Setup IAM user

The kops user will require the following IAM permissions to function properly:,One can use AWS console to create these group and policy or using the CLI also we can create.

AmazonEC2FullAccess

AmazonRoute53FullAccess

AmazonS3FullAccess

IAMFullAccess

AmazonVPCFullAccess

5.1 Create IAM group:
$aws iam create-group --group-name kops

5.2 Attach policy:
$aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name kops

$aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonRoute53FullAccess --group-name kops

$aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess --group-name kops

$aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMFullAccess --group-name kops

$aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonVPCFullAccess --group-name kops


5.3 Create IAM user: 

$aws iam create-user --user-name kops

5.4 Add user to group: 
$aws iam add-user-to-group --user-name kops --group-name kops

5.5 Create keys for the user: 

$aws iam create-access-key --user-name kops

{

    "AccessKey": {
    
        "UserName": "kops",
        "AccessKeyId": "XXXXXXXXXXXXXXXX
        "Status": "Active",
        "SecretAccessKey": "XXXXXXXXXXXXvmeKyXXsaOgtesn",
        "CreateDate": "2017-XX-09T03:25:32.426Z"
    }
    
}

5.6 Configure AWS CLI: 
$aws configure. Use SecretAccessKey and AccessKeyId

5.7 Create a S3 Bucket.

aws s3api create-bucket --bucket panchaleswar-k8s --region us-east-1 
or
aws s3api create-bucket --bucket panchaleswar-k8s --region us-east-2 --create-bucket-configuration LocationConstraint=us-east-2

5.8 Enable bucket versioning: 

$aws s3api put-bucket-versioning --bucket panchaleswar-k8s --region us-east-1 --versioning-configuration Status=Enabled

5.9 Set S3 bucket: 
$export KOPS_STATE_STORE=s3://panchaleswar-k8s

Set cluster name: 
$export NAME=cluster.k8s.local

6.Now Start Kubernetes cluster on AWS

$kops create cluster ${NAME} --zones us-east-1a --yes

7.To Delete the Cluster

$kops delete cluster --name ${NAME}

