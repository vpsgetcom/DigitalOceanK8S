This repo is a partially fork of https://github.com/digitalocean/terraform-provider-digitalocean

Install if not exist on your system: git, doctl, kubectl, terraform, helm 


Clone repo

```
git clone https://github.com/vpsgetcom/DigitalOceanK8S.git
cd DigitalOceanK8S
```

Set environment vars 
```
export  DIGITALOCEAN_ACCESS_TOKEN="796b73befb4542b6c12******************************************" #nix
setx DIGITALOCEAN_ACCESS_TOKEN "796b73befb4542b6c12******************************************"    #win

echo $DIGITALOCEAN_ACCESS_TOKEN #nix
echo %DIGITALOCEAN_ACCESS_TOKEN% #win
```


DigitalOcean authorize:

```
doctl account get
doctl auth list
```

Deploy k8s in DO:

```
terraform init
terraform plan
terraform apply

```

#OUT:
#cluster_name = "tf-k8s-fd8b29cca0"
#kubeconfig_path = "none"

Save kubectl config:

```
doctl kubernetes cluster kubeconfig save tf-k8s-fd8b29cca0
```

Check that there already echo app [hashicorp/http-echo] :
[described in \kubernetes-config\main.tf ]

```
kubectl get pods --namespace=test
 nginx-ingress-controller-55c67f759c-68k72                  1/1     Running   0          3m34s
 nginx-ingress-controller-default-backend-f569d8797-gzpbg   1/1     Running   0          3m34s
 test-7b6cc57b45-ll7kd                                      1/1     Running   0          3m46s
 test-7b6cc57b45-n9n4b                                      1/1     Running   0          3m46s
```

You should be able to access kubernetes dashboard from digitalocean portal, like: 
 https://cloud.digitalocean.com/kubernetes/clusters/.../.../#/deployment?namespace=_all

Also you may download kubeconfig file from DO portal and get your k8s FQDN: 
    server: https://3e0a2eef-f56f-4115-aff2-9bbc7d959c33.k8s.ondigitalocean.com


Check your LoadBalancer IP: 
```
get svc ingress-nginx-controller -o jsonpath="{.status.loadBalancer.ingress[0].ip}" --all-namespaces

 # 
 # nginx-ingress-controller                   LoadBalancer   10.245.82.255    159.***.2**.45   80:32105/TCP,443:30485/TCP   83m
 #
```
Check URL: http://159.***.2**.45/test 


-------


OK let's deploy some helm chart; for example elasticsearch.

Check helm version and go ahead.

```
helm version
 #version.BuildInfo{Version:"v3.5.4", GitCommit:"1b5edb69df3d3a08df77c9902dc17af864ff05d1", GitTreeState:"clean", GoVersion:"go1.15.11"}
```

```
helm init
helm repo add elastic https://helm.elastic.co
helm repo update
helm install elasticsearch elastic/elasticsearch  --namespace=test
```

Forward port from pod to localhost with kubectl forward
```
kubectl port-forward elasticsearch-master-0 9200:9200 --namespace=test
```
check:

```
curl http://localhost:9200/

```



