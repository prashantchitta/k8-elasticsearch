Elastic Search on Tess!
===================
This image is using Fabric8's great work around the [kubernetes plugin](https://github.com/fabric8io/elasticsearch-cloud-kubernetes) for elasticsearch and their [image](https://hub.docker.com/r/fabric8/elasticsearch-k8s/) as parent

Elasticsearch best-practices recommend to separate nodes in three roles:

 - Master nodes - intended for clustering management only, no data, no HTTP API 
 - Client nodes - intended for client usage, no data, with HTTP
 - API Data nodes - intended for storing and indexing your data, no HTTP API

We use 2 services as well:

 - One to target the client pods so that all requests to the cluster go
   through the client nodes
 - A headless service to list all cluster endpoints managed by RC's and petset

**Changes for PetSet**

Handle downscales so that no data loss occurs (using lifecycle hooks). For more info click [pre-stop-hook](https://github.com/jetstack/elasticsearch-pet)

**Volumes**
-----------
This uses Cinder volumes to mount to the data nodes. You can change the storage of cinder volumes as needed in `es-petset.yaml` in the section `storage: "100Gi"`

**Deploy**
----------
Given this, I'm going to demonstrate how to provision a production grade ES consisting of 3 master, 2 client and 3 data nodes
```
kubectl create -f es-client.yaml
kubectl create -f es-client-svc.yaml
kubectl create -f es-headless-svc.yaml
kubectl create -f es-master.yaml
kubectl create -f es-petset.yaml
```
Wait for all the pods to be in `Running` state.

To get the http end point of elasticsearch cluster, run
```
kubectl get services es-client --namespace=<namespace>
```
`EXTERNAL-IP` is the http endpoint for elasticsearch cluster and can be accessed from outside the cluster as well

**Scale**
---------
If you have to scale master/client/petset, just increase the replicas to the desired number
```
kubectl scale petset es-data --replicas=4 --namespace=kchitta
```
