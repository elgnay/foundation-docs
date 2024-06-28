# ManagedClusterAvailableUnknown

## Symptom
The ManagedCluster on the hub cluster has a condition of type `ManagedClusterConditionAvailable` with a `status` of `Unknown`.

You are able to check the status of this condition with command line below,
```
oc get managedcluster <cluster-name> -o jsonpath='{.status.conditions[?(@.type=="ManagedClusterConditionAvailable")].status}'
```

## Meaning
When the klusterlet agent starts running on the managed cluster, it updates a Lease resource in the cluster namespace on the hub cluster every N seconds, where N is configured in the `spec.leaseDurationSeconds` of the `ManagedCluster`. If the Lease resource is not updated in the past 5 * N seconds, the status of the `ManagedClusterConditionAvailable` condition for this managed cluster will be set to `Unknown`. Once the klusterlet agent connects back to the hub cluster and continues to update the Lease resource, the ManagedCluster will become available automatically.

## Impact
Once this issue happens, 
- Usally both the klusterlet agent and add-on agents cannot connect to the hub cluster. Changes on ManifestWorks and other add-on specific resources on the hub cluster can not be pulled to the managed cluster;
- The status of the `Available` condition of all `ManagedClusterAddOns` for this managed cluster will be set to `Unknown` as well;

## Diagnosis
The diagnosis instructions may vary and depend on the specific use case.
### Network segmentation between hub and managed cluster
### [Managed cluster not joined.](./ManagedClusterNotJoined.md)
### klusterlet is missing
### Klusterlet is broken
### Clock out-of-sync between hub and managed cluster
### Hub API server certificate changes
### Managed cluster destroied/hibernated.

If none of the above cases can help, please collect the ACM must-gather data of both hub cluster and the managed cluster for troubleshooting.

## Mitigation
