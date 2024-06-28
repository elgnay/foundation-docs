# The `ManagedClusterJoined` condition of `ManagedCluster` is not present

## Symptom
The ManagedCluster on the hub cluster has no condition of type `ManagedClusterJoined`. You are able to check the status of this condition with command line below,
```
oc get managedcluster <cluster-name> -o jsonpath='{.status.conditions[?(@.type=="ManagedClusterJoined")]}'
```

## Meaning
Either the [cluster importing](../../concepts/ClusterImporting.md#cluster-importing) or the [cluster registration](../../concepts/ClusterRegistration.md#cluster-registration) of this managed cluster is not finished sucessfully for some reason.

## Impact
Once this issue happens, the status of the condition `ManagedClusterConditionAvailable` for this managed cluster will become `Unknown` eventually. You are able to check the status of this condition with command line below,
```
oc get managedcluster <cluster-name> -o jsonpath='{.status.conditions[?(@.type=="ManagedClusterConditionAvailable")].status}'
```

## Diagnosis

### Cluster importing

For an [auto-imported cluster](../../concepts/ClusterImporting.md#auto-importing), check the status of the `ManagedClusterImportSucceeded` condtion.
```
oc get managedcluster <cluster-name> -o jsonpath='{.status.conditions[?(@.type=="ManagedClusterImportSucceeded")].status}'
```
If the status is not `True`, that means the auto-importing is not finished sucessfully and you can refer to [auto-importing faillure](./AutoImportingFailure.md) for the diagnosis instructions.

For a [manual-imported cluster](../../concepts/ClusterImporting.md#manual-importing), check if the existance of the resources below on the managed cluster.
- Klusterlet CR
```
oc get klusterlet klusterlet
```
- Klusterlet operator
```
oc -n open-cluster-management-agent get pod -l app=klusterlet
```

If any of the above resources is missing, you are able to recover them with the instructions from [Mitigation -> Reinstall the klusterlet](#reinstall-the-klusterlet)

### Cluster registration

1. Check the status of the Klusterlet CR on the managed cluster and see if there is any error in the conditions.
```
oc get klusterlet klusterlet -o yaml
```

Typical errros:
- [bootstrap hub kubeconfig is degraded](...)
- ... ...

2. Check the log of the klusterlet-agent on the managed cluster and see if there is any error.
```
oc -n open-cluster-management-agent logs -l app=klusterlet-agent
```

Typical errros:
- [connection timeout](...)
- [X509](...)
- ... ...

## Mitigation

### Reinstall the klusterlet
To reinstall the klusterlet on the managed cluster, follow the instructions from [Import cluster manually with CLI tool](../../guide/ManagedCluster/ManagedClusterManualImport.md).

## Required artifacts for further troubleshooting
If none of the above runbooks helps, please collect the ACM must-gather data of both hub cluster and the managed cluster for troubleshooting. If the must-gather data is not available for some reason, please collect the following information instead.
- On the hub cluster
  - YAML of the ManagedCluster
    ```
    oc get managedcluster <cluster-name> -o yaml > cluster.yaml
    ```
  - Log of the import controller
    ```
    oc -n multicluster-engine logs -l app=managedcluster-import-controller-v2 > import-controller.log
    ```
- On the managed cluster
  - YAML of Klusterlet;
    ```
    oc get klusterlet klusterlet -o yaml > klusterlet.yaml
    ```
  - Pod list in the `open-cluster-management-agent` namespace;
    ```
    oc -n open-cluster-management-agent get pods > agent-pods.txt
    ```
  - Secret list in the `open-cluster-management-agent` namespace;
    ```
    oc -n open-cluster-management-agent get secrets > agent-secrets.txt
    ```
  - Log of klusterlet pod;
    ```
    oc -n open-cluster-management-agent logs -l app=klusterlet > klusterlet.log
    ```
  - Log of klusterlet-agent pod;
    ```
    oc -n open-cluster-management-agent logs -l app=klusterlet-agent > klusterlet-agent.log
    ```
