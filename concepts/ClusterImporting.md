# Cluster importing

Cluster importing is the procedure to install the klusterlet, including both klusterlet-operator and klusterlet-agent, on the managed cluster. There are two types of cluster importing: auto-importing and manual-importing. They have different ways to install the klusterlet on the managed cluster.

### Auto-importing
The import controler running on the hub cluster will connect to the managed cluster and apply necessary resources on the managed cluster to install the klusterlet on the managed cluster. It means
- network connectivity from the hub cluster to the managed cluster is required;
- a kubeconfig of the managed cluster is in place on the hub cluster which can be used during the auto-importing precedure;

The auto-importing is enabled for a managed cluster if it is
- the `local-cluster`;
- or a cluster created with ACM, like a Hive provisioned cluster;
- or a cluster who has a auto-import secret in its cluster namespace. (the auto-import secret will be removed once the cluster joins the hub)

### Manual-importing
The user who is importing a cluster to the hub cluster runs the generated cluster importing command on the managed cluster mannually. Network connectivity from the hub cluster to the managed cluster is not required.