# Must-Gather APIServer Operator
A Kubernetes Operator for deploying MustGatherAPIServers (based on static-kas)

There is a published image for this operator that can be imported into your cluster runnning the following commands: 
(The REPLACE_ME placeholder should be replaced with an HTTP(S) URL to download the Zip of the MustGather)
```bash
➤ oc adm policy add-cluster-role-to-user cluster-admin -z default
➤ kubectl apply -f https://raw.githubusercontent.com/MichaelWasher/must-gather-server-operator/main/config/crd/bases/mustgatherserver.michael-washer.com_mustgatherapiservers.yaml    
➤ kubectl run --image quay.io/mwasher/mustgatherapiserveroperator:0.0.1 mustgatherapiserveroperator
➤ cat <<EOF | k apply -f -
apiVersion: mustgatherserver.michael-washer.com/v1alpha1
kind: MustGatherAPIServer
metadata:
  labels:
    app: must-gather-server
  name: must-gather-server
spec:
  mustgatherurl: REPLACE_ME
EOF
```

This will start Pods similar to the following:
```bash
must-gather-server-operator ➤ k get pods
NAME                                                      READY   STATUS    RESTARTS      AGE
must-gather-server-mustgatherapiserver-5c8fc67449-nh8qj   1/1     Running   0             21s
mustgatherapiserveroperator                               1/1     Running   1 (31m ago)   34m
```

***Note**: This is insecure and will be replaced with an alternative approach shortly.*

To access the cluster, the following can be used:
```bash
➤ kubectl port-forward svc/must-gather-server 8080:8080 &
➤ cat <<EOF >/tmp/kk
apiVersion: v1
clusters:
- cluster:
    server: http://localhost:8080
  name: static-kas
contexts:
- context:
    cluster: static-kas
    namespace: default
  name: static-kas
current-context: static-kas
kind: Config
EOF
➤ alias omg="kubectl --kubeconfig /tmp/kk "
```