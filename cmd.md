
cd ~/go/src/kubernetes-v1.15.5/

cluster/kubectl.sh taint nodes --all node-role.kubernetes.io/master-

helm init --history-max 200

for i in ~/study/download/istio-1.1.7/install/kubernetes/helm/istio-init/files/crd*yaml; do cluster/kubectl.sh apply -f $i; done

cat <<EOF | cluster/kubectl.sh apply -f -
   apiVersion: v1
   kind: Namespace
   metadata:
     name: istio-system
     labels:
       istio-injection: disabled
EOF

cluster/kubectl.sh apply -f istio-lean.yaml

cluster/kubectl.sh taint nodes --all node-role.kubernetes.io/master-

cluster/kubectl.sh apply -f istio-local-gateway.yaml

cluster/kubectl.sh apply --selector knative.dev/crd-install=true --filename https://github.com/knative/serving/releases/download/v0.9.0/serving.yaml

cluster/kubectl.sh apply --filename https://github.com/knative/serving/releases/download/v0.9.0/serving.yaml 
