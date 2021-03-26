# Kong API Gateway Ingress Controller With Istio Service Mesh

```bash
region=us-east1 

machine_type=n1-standard-1

gcloud container clusters \
    create devops25 \
    --region $region \
    --machine-type $machine_type \
    --enable-autoscaling \
    --num-nodes 1 \
    --max-nodes 3 \
    --min-nodes 1
```

```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.9.2
./bin/istioctl install
```

```bash
kubectl label namespace default istio-injection=enabled
kubectl create namespace kong
kubectl label namespace kong istio-injection=enabled
```
```bash
kubectl apply -f https://bit.ly/k4k8s
kubectl get pods -n kong
kubectl get svc -n kong
export PROXY_IP=<LOADBALANCER_IP>
curl -i $PROXY_IP
```

```bash
kubectl apply -f https://bit.ly/k8s-httpbin
