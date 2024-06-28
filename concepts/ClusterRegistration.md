# Cluster registration
Once the klusterlet is installed on the managed cluster, the klusterlet-agent will start the cluster registration procedure with the bootstrap hub kubeconfig. 
A new hub kubeconfig with a client certificate will be created during the cluster regisration procedure, and it will be used for the upcoming communication between the managed cluster and the hub cluster.