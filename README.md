$ brew install jq
$ brew install awscli
$ aws configure
$ brew install terraform
$ brew install kubernetes-cli
$ brew install kops

$ aws iam create-group --group-name kops

$ aws iam attach-group-policy \
		--policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess \
		--group-name kops

$ aws iam attach-group-policy \
		--policy-arn arn:aws:iam::aws:policy/AmazonRoute53FullAccess \
		--group-name kops

$ aws iam attach-group-policy \
		--policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess \
		--group-name kops

$ aws iam attach-group-policy \
		--policy-arn arn:aws:iam::aws:policy/IAMFullAccess \
		--group-name kops

$ aws iam attach-group-policy \
		--policy-arn arn:aws:iam::aws:policy/AmazonVPCFullAccess \
		--group-name kops

$ aws iam create-user --user-name kops

$ aws iam add-user-to-group --user-name kops --group-name kops

$ aws iam create-access-key --user-name kops > kops-access-key.json

$ export AWS_ACCESS_KEY_ID=$(cat kops-access-key.json \
		| jq '.AccessKey.AccessKeyId' \
		| sed -e 's/^"//' -e 's/"$//')

$ export AWS_SECRET_ACCESS_KEY=$(cat kops-access-key.json \
		| jq '.AccessKey.SecretAccessKey' \
		| sed -e 's/^"//' -e 's/"$//')

$ aws s3api create-bucket \
		--bucket lucidfrontier-cloud-k8s-state-store \
		--region us-east-1

$ aws s3api put-bucket-versioning \
		--bucket lucidfrontier-cloud-k8s-state-store   \
		--versioning-configuration Status=Enabled

$ aws ec2 create-key-pair --key-name k8ts

If no `~/.ssh/id_rsa.pub`
$ ssh-keygen -t rsa -f ~/.ssh/id_rsa

$ kops create secret \
		--state s3://lucidfrontier-cloud-k8s-state-store
		--name k8s-cluster.lucidfrontier.cloud sshpublickey admin -i ~/.ssh/id_rsa.pub 

$ kops create cluster k8s-cluster.lucidfrontier.cloud \
		--state s3://lucidfrontier-cloud-k8s-state-store \
		--zones us-east-1a \
		--yes

$ kops validate cluster \
		--state s3://lucidfrontier-cloud-k8s-state-store

$ kops delete cluster \
		--name k8s-cluster.lucidfrontier.cloud \
		--state s3://lucidfrontier-cloud-k8s-state-st
		--yes


