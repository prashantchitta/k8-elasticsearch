Elastic Search on Tess with emptyDir!
===================
The specs here does not use petset for data nodes and depends on a replication controller.
This also does not use persistent volumes. It uses `emptyDir`. Read more about [emptyDir](https://kubernetes.io/docs/user-guide/volumes/#emptydir) in case of questions
