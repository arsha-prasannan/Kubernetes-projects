# Worpress in kubernetes with NFS

We can deploy wordpress domain in Kuberenets. Here, I have created a cluster in GKE and deployed the domain.  The workflow for the project is :
### 1. Created a Kubernetes cluster in GKE 

I have created a standard GKE cluster for implementing the wordpress domain. It's a 3 node zonal cluster using ubuntu  with conatinerd image in the node. 
The cluster can be accessed either through cloudshell or by generating kubeconfig in local end after installing gcloud SDK. we can use the kubectl commands for creating and working with the resources.

### 2. Deployed an nginx ingress controller
After the cluster creation, the Nginx ingress controller with a load balancer service has been created.


### 3. Created an instance in cloudsql for the database
A SQL instance has been spinned up in the cloudsql and created a database for the wordpress domain.

### 4. Created a zone in cloudDNS and added the NS in GoDaddy.
For exposing the domain to the public , I have first created a zone in cloudDNS. Then Purchased a domain from godaddy and added the NS from cloudDNS. Created an A record to get pointed to the ingress LoadBalancer.

### 5. Deployed a cert-manager for SSL in domain
Here I have used letsencrypt for securing the domain with SSL. After creating the resources need for cert-manger i have created a cluster-issuer.

### 6. NFS creation and Mounting
In order to make the document root files persistant we can either use a PV & PVC . But here I have created an instance from the filestore and mounted it as NFS mount on all the nodes. For automating mounting i have used an user-data script and added to the template for the nodes. The Document root files with wp-config have the crenetials to access the database is placed in that mounted directory so that it can be added as the volume to our wordpress pods.

### 7. Created the deployment, services and ingress rule for the wordpress.
The wordpress deployment has been created with official docker image(for securing the image has been pulled from the docker hub, then retagged and uploaded to gcr). The deployment has been exposed using a service for type:clusterIP. An ingress rule with host as the wordpress domain and backend as the clusterIP serice has been created. Corresponding TLS entries and annotations for SSL are also added.
The domain was accessible from the browser with SSL enabled.

### 8. Deployed Adminer in kubernetes for accessing the database.

An adminer is also deployed for accessing the database. I have craeted a deployment , service of type cluster IP and ingress rule for public access for the adminer in the same way as the wordpresss. The adminer was made publically available by adding a subdomain in the cloudDNS zone as adminer.example.com
