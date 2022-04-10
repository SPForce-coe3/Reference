# Table of contents

- 개발환경
  - [Installing kubectl](#Installing kubectl)

# Installing kubectl
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html

# Installing eksctl
https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/eksctl.html

$ aws eks --region [리전명] update-kubeconfig --name [EKS 이름]
예 : aws eks --region ap-northeast-2 update-kubeconfig --name ssep-poc-cluster

$ eksctl create cluster --name ssep-poc-cluster --version 1.21 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 1 --nodes-max 3

