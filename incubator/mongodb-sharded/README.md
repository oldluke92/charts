# Sharded mongodb for Kubernetes

This repo contains a helm chart to deploy a sharded mongodb using mongos query router and mongodb-replicasets.

## Requirements

* Helm 2.8+
* Kubernetes 1.8+

## Setup

```sh
helm install incubator/mongodb-sharded --name mongodb
```

You can also provide custom options via --set or providing a values file, for more Information reference values.yaml and the documentation of [stable/mongodb-replicaset](https://github.com/helm/charts/tree/master/stable/mongodb-replicaset).

## Authentication

If you want to enable authentication, you need to set authentication to enabled **in all three parts** of the values.yml
You also MUST set the following values (you can omit metricsUser and Password if you disable metrics for the shardcluster):

```yml
auth:
  enabled: true
  key: "thisisasupersecretmongosecret"

mongodb-replicaset-configdb:
  auth:
    enabled: true
    # beware! This will be THE cluster admin account later.
    adminUser: admin
    adminPassword: secretpassword
    ## This is exemplary for the key secret if your release is named "mongodb-sharded".
    ## If your release is named "mongodb-sharded" it would be mongodb-sharded-keyfile
    ## Elseways it would be [ReleaseName]-mongodb-sharded-keyfile
    existingKeySecret: mongodb-sharded-keyfile

  auth:
    enabled: true
    # This can (and should) be different from the Cluster-Admin account and can used on the single shard only
    # It can also be different for different shards
    adminUser: admin
    adminPassword: secretpassword
    ## This is exemplary for the key secret if your release is named "mongodb-sharded".
    ## If your release is named "mongodb-sharded" it would be mongodb-sharded-keyfile
    ## Elseways it would be [ReleaseName]-mongodb-sharded-keyfile
    existingKeySecret: mongodb-sharded-keyfile
    metricsUser: metrics
    metricsPassword: secretmetricspassword
```

WARNING: If you choose a long release name, the mongodb-replicasets may not be able to create pods as these names are limited to 63 characters (by DNS convention).

You may want to set `fullnameOverride` for both of the replicasets because of that.

Elseways the pod names will be
[releaseName]-mongodb-replicaset-[replicasetName]-[replicaNumber].

## Knowledge

This chart is based upon the information from:

* [https://pauldone.blogspot.com/2017/07/sharded-mongodb-kubernetes.html](https://pauldone.blogspot.com/2017/07/sharded-mongodb-kubernetes.html)
* [https://github.com/gangchen03/refarch-icp-mongodb](https://github.com/gangchen03/refarch-icp-mongodb)
* [https://github.com/helm/charts/tree/master/stable/mongodb-replicaset](https://github.com/helm/charts/tree/master/stable/mongodb-replicaset)