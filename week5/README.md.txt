#Assignment2: Create K8s cluster, 
#deploy containerized stateless applications using K8s manifests, 
#expose the applications as NodePort services, roll out an updated version of the application


#Deploy ec2 instance with Terraform


#Copy deployment files into ec2 instance 
scp -i week5 init_kind.sh kind.yaml msql_dep.yaml msql_pd.yaml msql_replica.yaml msql_ser.yaml wbap_dep.yaml wbap_pd.yaml wbap_replica.yaml wbap_serv.yaml [public IP]:/tmp


#login into the eC2 instance and cd /tmp
ssh -i [keyname] [public IP]


#Run Chmod 777 to set permission init_kind.sh


#Create the Kind

- ./init_kind.sh

#Pull docker images from ECR
#Login ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 846767006554.dkr.ecr.us-east-1.amazonaws.com
docker pull 846767006554.dkr.ecr.us-east-1.amazonaws.com/mysql:v1.0 {mysql}
docker pull 846767006554.dkr.ecr.us-east-1.amazonaws.com/mywebapp:v1.0 [webserver]


#Create Namespace 
alias k=kubectl
- k create ns winter2023

#K get pod/mysql -n winter2023 -o yaml, [you will find ip address of mysql pod]
#Update IP address of mysql on pod and deployment of web app
#Deploy pod, deployment and service for web app
#k get all -n winter2023


# deploy mysql replicaset,pod,deployment & service

- k apply -f msql_pd.yaml -n winter2023
- k apply -f msql_dep.yaml -n winter2023
- k apply -f msql_replica.yaml -n winter2023
- k apply -f msql_ser.yaml -n winter2023

#deploy webapp replicaset,pod,deployment & service
- k apply -f wbap_pd.yaml -n winter2023
- k apply -f wbap_dep.yaml -n winter2023
- k apply -f wbap_replica.yaml -n winter2023
- k apply -f wbap_serv.yaml -n winter2023



# Run port forwarding and curl local host
- k port-forward service/employees  8084:8080 -n winter2023
- curl localhost:8084
 


#Run the web-application through browser @publicIP:30000


#Update the image version in the deployment manifest 
 - k edit deployment.apps/employees -n winter2023 [chnage from 1.0 to any version]


#Print the new version running in the cluster
 - kubectl get pods -n winter2023



