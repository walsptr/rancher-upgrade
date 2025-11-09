# rancher-upgrade


```
After the cluster-autoscale-demo RKE2 cluster provisioning completed, switch to Cluster Explorer. Navigate to menu Apps > Repositories. Add a new repo:
name: autoscaler
repo: https://kubernetes.github.io/autoscaler
```

```
After the repo is added, deploy the autoscaler helm chart on the RKE2 cluster
namespace: kube-system
project : System
helm chart values override as below.
autoDiscovery:
  clusterName: cluster-autoscale-demo

cloudConfigPath: /config/cloud-config
cloudProvider: rancher

extraVolumeSecrets:
  cluster-autoscaler-cloud-config:
    mountPath: /config
    name: cluster-autoscaler-cloud-config
```

```
machineDeploymentAnnotations:
  cluster.provisioning.cattle.io/autoscaler-max-size: '3'
  cluster.provisioning.cattle.io/autoscaler-min-size: '1'

These annotations should be positioned like this part of the YAML.
    machinePools:
      - controlPlaneRole: true
        dynamicSchemaSpec: >-
          ....(removed for sanity)
        etcdRole: true
        machineConfigRef:
          kind: VmwarevsphereConfig
          name: nc-cluster-autoscale-demo-master-ccv47
        machineOS: linux
        name: master
        quantity: 1
        unhealthyNodeTimeout: 0s
      - dynamicSchemaSpec: >-
          ....(removed for sanity)
        machineConfigRef:
          kind: VmwarevsphereConfig
          name: nc-cluster-autoscale-demo-worker-9pzc9
        machineDeploymentAnnotations:
          cluster.provisioning.cattle.io/autoscaler-max-size: '3'
          cluster.provisioning.cattle.io/autoscaler-min-size: '1'
        machineOS: linux
        name: worker
        quantity: 1
        unhealthyNodeTimeout: 0s
        workerRole: true

```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sleep
  labels:
    app: sleep
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sleep
  template:
    metadata:
      labels:
        app: sleep
    spec:
      containers:
        - image: wardsco/sleep
          name: sleep
          imagePullPolicy: IfNotPresent
          resources:
            requests:
              cpu: 1
```
