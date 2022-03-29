# Instructions

## Without persistent volume

### Create Mysql Deployment
kubectl apply -f mysql-deployment.yml

### Create Mysql Service to expose it to other pods in the cluster
kubectl apply -f mysql-service.yml

### Create Wordpress Deployment
kubectl apply -f wordpress-deployment.yml

### Create Wordpress Service to expose it to public internet and within the cluster
kubectl apply -f wordpress-service.yml

Get the public IP address of the node where the wordpress pod got created.
Go to the browser and open <public-ip-of-the-node>:<nodePort-of-wordpress-service>
ex:
   http://123.456.789.0:30000

## With persistent volume

### Create local directory for the volume in the node mentioned in 
nodeAffinity.required.nodeSelectorTerms.matchExpressions[0].values

### Create Persistent Volume
kubectl apply -f volumes.yml

### Create Persistent Volume Claim
kubectl apply -f pvclaims.yml

### Create Mysql Deployment
kubectl apply -f mysql-deployment.yml

### Create Mysql Service to expose it to other pods in the cluster
kubectl apply -f mysql-service.yml

### Create Wordpress Deployment
kubectl apply -f wordpress-deployment.yml

### Create Wordpress Service to expose it to public internet and within the cluster
kubectl apply -f wordpress-service.yml

Get the public IP address of the node where the wordpress pod got created.
Go to the browser and open <public-ip-of-the-node>:<nodePort-of-wordpress-service>
ex:
   http://123.456.789.0:30000