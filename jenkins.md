#### Table of contents
  - AWS EBS CSI Driver 를 AWS EKS 클러스터에 배포
  - Install Jenkins with Helm v3
  - Reference

### AWS EBS CSI Driver 를 AWS EKS 클러스터에 배포
  - #### AWS IAM 정책 생성 (iam > 액세스관리 > 정책 )
    - edp.emc.jenkins.policy
    ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "ec2:AttachVolume",
              "ec2:CreateSnapshot",
              "ec2:CreateTags",
              "ec2:CreateVolume",
              "ec2:DeleteSnapshot",
              "ec2:DeleteTags",
              "ec2:DeleteVolume",
              "ec2:DescribeAvailabilityZones",
              "ec2:DescribeInstances",
              "ec2:DescribeSnapshots",
              "ec2:DescribeTags",
              "ec2:DescribeVolumes",
              "ec2:DescribeVolumesModifications",
              "ec2:DetachVolume",
              "ec2:ModifyVolume"
            ],
            "Resource": "*"
          }
        ]
      }
    ```
  - #### AWS IAM 자격 증명 공급자 생성 (iam > 액세스관리 > 자격 증명 공급자 )
    - 공급자 유형 : OpenID Connect
    - 공급자 URL  : EKS Cluster 의 OpenID 공급자 URL 입력 후 "지문 가져오기" 클릭
    - 대상 : sts.amazonaws.com
    
  - #### 앞서 생성한 정책을 연결하여 IAM 역할을 생성함. (iam > 액세스관리 > 역할 )
    - 1 단계 : 신뢰할 수 있는 엔터티 선택   
     ![image](https://user-images.githubusercontent.com/80744273/167753398-280f9370-d8d0-4af6-8f09-575d79bd79df.png)
    - 2 단계 : 권한 추가   
      edp.emc.jenkins.policy 선택   
      
    - 3 단계 :   
      역할 이름 : edp.emc.jenkins.ebs-csi-role
    - 생성된 역할의 신뢰관계 편집   
      수정 부분 : aud": "sts.amazonaws.com" → sub": "system:serviceaccount:kube-system:ebs-csi-controller-sa"
      ```
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {
                        "Federated": "arn:aws:iam::075134174875:oidc-provider/oidc.eks.ap-northeast-2.amazonaws.com/id/7B416363991657EAA13B94AB447BCE89"
                    },
                    "Action": "sts:AssumeRoleWithWebIdentity",
                    "Condition": {
                        "StringEquals": {
                            "oidc.eks.ap-northeast-2.amazonaws.com/id/7B416363991657EAA13B94AB447BCE89:sub": "system:serviceaccount:kube-system:ebs-csi-controller-sa"
                        }
                    }
                }
            ]
        }      
      ```
  - #### EBS CSI Driver 배포
   ```
   git clone https://github.com/kubernetes-sigs/aws-ebs-csi-driver.git   
   cd aws-ebs-csi-driver/deploy/kubernetes
   ```
  - #### serviceaccount-csi-controller.yaml파일에 annotations를 추가   
   역할의 권한을 ServiceAccount가 사용할 수 있도록 생성한 역할의 arn을 추가한다.
   ```
    ---
    # Source: aws-ebs-csi-driver/templates/serviceaccount-csi-controller.yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: ebs-csi-controller-sa
      labels:
        app.kubernetes.io/name: aws-ebs-csi-driver
      #Enable if EKS IAM for SA is used
      annotations:
        eks.amazonaws.com/role-arn: arn:aws:iam::075134174875:role/edp.emc.jenkis.ebs-csi-role   
   ```
  - #### EBS CSI Driver를 클러스터에 배포
  ```
  kubectl apply -k aws-ebs-csi-driver/deploy/kubernetes/base
  ```
#### Install Jenkins with Helm v3

  - #### Create a namespace
    ```
    $ kubectl create namespace jenkins
    ```
    
  - #### Create a persistent volume
    - jenkins-pvc.yaml 작성
    ```
    # jenkins-pvc.yml
    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
      name: jenkins-sc
    provisioner: ebs.csi.aws.com
    volumeBindingMode: WaitForFirstConsumer

    ---
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: jenkins-pvc
      namespace: jenkins
    spec:
      accessModes:
        - ReadWriteOnce
      storageClassName: jenkins-sc
      resources:
        requests:
          storage: 30Gi    
    ```
    - Run the following command to apply the spec     
    ```
    $ kubectl apply -f jenkins-pvc.yaml    
    ```     

 - #### Create a service account   
   - jenkins-sa.yaml 작성   
   https://raw.githubusercontent.com/installing-jenkins-on-kubernetes/jenkins-sa.yaml
   
    ```
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: jenkins
      namespace: jenkins
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      annotations:
        rbac.authorization.kubernetes.io/autoupdate: "true"
      labels:
        kubernetes.io/bootstrapping: rbac-defaults
      name: jenkins
    rules:
    - apiGroups:
      - '*'
      resources:
      - statefulsets
      - services
      - replicationcontrollers
      - replicasets
      - podtemplates
      - podsecuritypolicies
      - pods
      - pods/log
      - pods/exec
      - podpreset
      - poddisruptionbudget
      - persistentvolumes
      - persistentvolumeclaims
      - jobs
      - endpoints
      - deployments
      - deployments/scale
      - daemonsets
      - cronjobs
      - configmaps
      - namespaces
      - events
      - secrets
      verbs:
      - create
      - get
      - watch
      - delete
      - list
      - patch
      - update
    - apiGroups:
      - ""
      resources:
      - nodes
      verbs:
      - get
      - list
      - watch
      - update
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      annotations:
        rbac.authorization.kubernetes.io/autoupdate: "true"
      labels:
        kubernetes.io/bootstrapping: rbac-defaults
      name: jenkins
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: jenkins
    subjects:
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:serviceaccounts:jenkins
    ```
    
   - Run the following command to apply the spec
    ```
      $ kubectl apply -f jenkins-sa.yaml
    ```
 - ### Install Jenkins
   - #### Install Helm v3
    ```
    $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    $ chmod 700 get_helm.sh
    $ ./get_helm.sh
    ```
   - #### Configure Helm
    ```
    $ helm repo add jenkinsci https://charts.jenkins.io
    $ helm repo update
    $ helm search repo jenkinsci
    ```

   - jenkins-values.yaml 작성하기   
     https://raw.githubusercontent.com/jenkinsci/helm-charts/main/charts/jenkins/values.yaml   
   
      - LoadBalancer  수정하기
      ```
      serviceType: LoadBalancer
      # Jenkins controller service annotations
      serviceAnnotations: 
          service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
          service.beta.kubernetes.io/aws-load-balancer-subnets: subnet-0d8d50653b7935fee,subnet-088cab1b1e8d2966c
      ```
      - persistence 수정하기
      ```
      persistence:  
        enabled: true
        existingClaim: jenkins-pvc  
        storageClass: jenkins-sc
      ```
      - serviceAccount 수정하기      
      ```
      serviceAccount:
        create: false
        # The name of the service account is autogenerated by default
        name: jenkins
        annotations: {}
        imagePullSecretName:
      ```
    
   - Run the following command to apply the spec   
    ```
    helm install jenkins -n jenkins -f jenkins-values.yml jenkinsci/jenkins
    ```
   - Get your 'admin' user password by running:   
    ```
    kubectl exec --namespace jenkins -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo
    ```
    
#### Reference
 - https://velog.io/@aylee5/EKS-Helm%EC%9C%BC%EB%A1%9C-Jenkins-%EB%B0%B0%ED%8F%AC-MasterSlave-%EA%B5%AC%EC%A1%B0-with-Persistent-VolumeEBS
 - https://aws.amazon.com/ko/blogs/storage/deploying-jenkins-on-amazon-eks-with-amazon-efs/
 - https://nyyang.tistory.com/113
 - https://www.jenkins.io/doc/book/installing/kubernetes/
 - https://www.eksworkshop.com/intermediate/210_jenkins/deploy/
