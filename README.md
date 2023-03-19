# Demo

The goal is to deploy a simple application in Linode. 


## Creating K8s Cluster in Linode

We first craete a Kubernetes cluster:

![image](https://user-images.githubusercontent.com/18715119/226173774-fad33b4e-4bb0-4ac1-8744-1d01a26cd349.png)

We fill the information as below and define the number of worker nodes:

![image](https://user-images.githubusercontent.com/18715119/226173955-7321056b-bb01-4a97-b804-a1ac8a4841d7.png)

After a while we can see the nodes being created on our dashboard:

![image](https://user-images.githubusercontent.com/18715119/226174949-0e4a38ce-11f8-42ed-943b-fc6a95e8d8fa.png)

We can then download `test-kubeconfig.yaml` to be able to access the cluster from our machine. We then set it as an environemnt variable in our terminal.

    export KUBECONFIG=test-kubeconfig.yaml
    
    # We can then check if the nodes are recognized.
    ❯ kubectl get node
      # NAME                           STATUS   ROLES    AGE   VERSION
      # lke98572-148465-6416fe515b62   Ready    <none>   38m   v1.25.4
      # lke98572-148465-6416fe51b540   Ready    <none>   38m   v1.25.4

## Deploy MongoDB StatefulSet
We can either create all the cofiguration files ourself or use a bundle of config files via Helm. Just search for `MongoDB Helm chart` in this case. A maintained char is kept [here](https://github.com/bitnami/charts).

    # We can run the assocaited command to add the helm repository 
    # The command executes against the cluster that we are connected to (Linode in this case)
    helm repo add bitnami https://charts.bitnami.com/bitnami
        # "bitnami" has been added to your repositories
    
    # To check all the available charts on the repo
    helm search repo bitnami
    
    helm search repo bitnami/mongo
        # NAME                   	CHART VERSION	APP VERSION	DESCRIPTION                                       
        # bitnami/mongodb        	13.9.1       	6.0.5      	MongoDB(R) is a relational open source NoSQL da...
        # bitnami/mongodb-sharded	6.3.1        	6.0.5      	MongoDB(R) is an open source NoSQL database tha...

We can find the parameters that we can modify to set our MongoDB [here](https://github.com/bitnami/charts/tree/main/bitnami/mongodb).

![image](https://user-images.githubusercontent.com/18715119/226177961-5dd23851-30fe-4c87-a699-aba02dd257ad.png)

We set `architecture` parameter to `replicaset` since we want multiple replicas of the database. We can also use `auth.rootPassword` to set the root password. To set these parameters we need a values file: `test-mongodb-values.yaml`. Using that value file we can install MongoDB:

    helm install mongodb --values test-mongodb-values.yaml bitnami/mongodb
    
    kubectl get pod
        # NAME                READY   STATUS              RESTARTS   AGE
        # mongodb-0           1/1     Running   0          2m3s
        # mongodb-1           1/1     Running   0          77s
        # mongodb-2           0/1     Running   0          25s
        # mongodb-arbiter-0   1/1     Running   0          2m2s
    
    # We can get all created objects
    kubectl get all
    
    kubectl get secret
    
The worker nodes can be seen in Linode:

![image](https://user-images.githubusercontent.com/18715119/226184175-69ed6ce5-2434-40fb-b52f-f53f95f75222.png)

Under volumes we can see 3 persistent components:

![image](https://user-images.githubusercontent.com/18715119/226184287-5fbe591e-627c-44aa-adae-910305236e51.png)

For each of the 3 pods a physical storage was created due to the vlaues that we provided in the yaml file.

## Deploy MongoExpress

Since there would be one pod and 1 service, the configuration file is manually created.
