# ⛹️ Lab - Task that clones github repository
We need to create a PersistentVolume and PersistentVolumeClaim.

Tekton Persistent Volume - openshift-tekton-pv.yml
```
# Reserve 500 MB disk space for Persistent Volume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: openshift-tekton-pv
  labels:
     app: tekton
spec:
  capacity:
    storage: 500Mi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain 
  nfs: 
    server: "192.168.1.80"
    path: "/mnt/maven"
```

Create the Persistent Volume
```
oc apply -f openshift-tekton-pv.yml
```

TekTon Persistent Volume Claim - openshift-tekton-pvc.yml
```
# Request for 500 disk space from one of the Persistent Volume
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: openshift-tekton-pvc
  labels:
     app: tekton
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 500Mi
```

Create the Persistent Volume Claim
```
oc apply -f openshift-tekton-pvc.yml
```

We need to install the git-clone  and maven tasks from Tekton Hub.  For more details refer https://hub.tekton.dev/tekton/task/git-clone
```
tkn hub install task git-clone
tkn hub install task maven
```

Create the Java CI/CD Pipeline
```
cd ~
git clone http://gitea-demo.example.com/jegan/tekton-triggers-demo.git
cd tekton-triggers-demo
oc apply -f java-tekton-cicd-pipeline.yml 
```

Let's trigger
