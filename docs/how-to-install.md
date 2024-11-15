# How to install

## Prerequisites

### Cluster requirements

TODO 

Add here cluster requirements ( eg. 1 host cluster with N nodes , 2 member clusters with N nodes each .. ) and cluster configuration instructions

### Software requirements
Openshift cluster - ( tested with OCP, OSD, Rosa and CRC )

## Install Host Operator

Use [ksctl CLI](https://github.com/kubesaw/ksctl) to install the host operator:

```bash
ksctl adm install-operator host --kubeconfig <path/to/host/kubeconfig> --namespace toolchain-host-operator
```

The command will create the namespace (if it doesn’t exist) and deploy the CatalogSource, OperatorGroup and Subscriptions for the latest version of the host operator


## Install Member Operator

Use [ksctl CLI](https://github.com/kubesaw/ksctl) to install the member operator:

```bash
ksctl adm install-operator member --kubeconfig <path/to/member/kubeconfig> --namespace <namespace>
```

The command will create the namespace (if it doesn’t exist) and deploy the CatalogSource, OperatorGroup and Subscriptions for the latest version of the member operator.

You can repeat this command for all the member clusters you have on which you want to deploy the member operator.

## Register member clusters

Use [ksctl CLI](https://github.com/kubesaw/ksctl) to register a member cluster in the host operator:

```bash
ksctl adm register-member --host-kubeconfig <host/kubeconfig> --member-kubeconfig <member/kubeconfig>
```

Note: if the cluster uses let’s encrypt certificates the –lets-encrpyt flag should be provided to the above command