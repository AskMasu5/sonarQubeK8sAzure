# sonarQubeK8sAzure
Prerequisites
Bash/PowerShell terminal with kubectl installed [Azure Cloud Shell with kubectl pre-installed]
PostgreSQL database to store SonarQube’s data [Azure Database for PostgreSQL]
Kubernetes cluster [Azure Kubernetes Service]

Stuff to create :
1. One Sonar container
2. One Postgres container
3. Kubernetes deployment files for both services.
4. Kubernetes service files for both services.
5. Persistent storage for the database.
6. A Kubernetes cluster. 
7. Secret for storing the database password.

Create PersistentVolumeClaims to store SonarQube plugin data
AKS comes with built-in Storage Classes for HDD (default) and SSD (premium) storage:
$kubectl get sc

Create 2 PVCs since SonarQube uses two locations to store the data:  /opt/sonarqube/data/ and /opt/sonarqube/extensions/
sonar-data.yaml

PVC for Sonar’s extensions directory
sonar-extensions.yaml

Create those PVCs:
$kubectl apply -f sonar-data.yaml
$kubectl apply -f sonar-extensions.yaml

Create a Secret to store PostgreSQL password
Kubernetes has a built-in capability to store secrets, create a secret you need to base64 encode a secret value
$ echo -n 'mediumpostgres' | base64
bWVkaXVtcG9zdGdyZXM=

and create a k8s secret using YAML file
sonar-secret.yaml

Create a SonarQube deployment
After creating PVCs and Postgres secret we are ready to deploy using the following YAML file
sonar-deployment.yaml
$ kubectl apply -f sonar-deployment.yaml

Publish SonarQube via Kubernetes Service
After SonarQube is up and running we need to create a public endpoint to access our service
sonar-svc.yaml

$ kubectl apply -f sonar-svc.yaml
service/sonarqube created
$ kubectl get svc
NAME        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
sonarqube   LoadBalancer   10.0.7.213     54.164.220.88   80:31284/TCP   13m

Now we can hit EXTERNAL-IP address and login to SonarQube
https://54.164.220.88/projects