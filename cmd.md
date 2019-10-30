```
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

cluster/kubectl.sh apply -f ~/study/download/istio-1.1.7/istio-lean.yaml

cluster/kubectl.sh taint nodes --all node-role.kubernetes.io/master-

cluster/kubectl.sh apply -f ~/study/download/istio-1.1.7/istio-local-gateway.yaml

cluster/kubectl.sh apply --selector knative.dev/crd-install=true --filename https://github.com/knative/serving/releases/download/v0.9.0/serving.yaml

cluster/kubectl.sh apply --filename https://github.com/knative/serving/releases/download/v0.9.0/serving.yaml  
cluster/kubectl.sh get po -Aw
  
cluster/kubectl.sh apply -f ~/study/test-deploy/go/helloworld-go/service.yaml  

if  cluster/kubectl.sh get configmap config-istio -n knative-serving &> /dev/null; then
       INGRESSGATEWAY=istio-ingressgateway
fi

export IP_ADDRESS=$(cluster/kubectl.sh get node  --output 'jsonpath={.items[0].status.addresses[0].address}'):$(cluster/kubectl.sh get svc $INGRESSGATEWAY --namespace istio-system   --output 'jsonpath={.spec.ports[?(@.port==80)].nodePort}')

curl -H "Host: helloworld-go.default.example.com" http://${IP_ADDRESS} -v -w "%{time_total}"   

```
