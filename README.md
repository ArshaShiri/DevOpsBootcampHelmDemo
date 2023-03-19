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
