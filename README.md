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
{"message":"no Route matched with those values"}%                                                                
```

```bash
kubectl apply -f https://bit.ly/k8s-httpbin
kubectl apply -f 100-ingress.yaml
curl -i $PROXY_IP/get
curl -i --data "foo=bar" -X POST $PROXY_IP/post
kubectl apply -f 200-plugin.yaml 
kubectl apply -f 201-ingress.yaml
curl -i $PROXY_IP/get
{"message":"Unauthorized"}
```

```bash
kubectl apply -f 300-consumers.yaml -n kong
kubectl create secret -n kong \
  generic app-secret  \
  --from-literal=kongCredType=jwt  \
  --from-literal=key="admin" \
  --from-literal=algorithm=HS256 \
  --from-literal=secret="HARD_SCERET"
```

Get the token in jwt.io using above secret, `kid` as `key` in header, and `exp` in payload
```bash
Header
{
  "alg": "HS256",
  "typ": "JWT",
  "kid" : "admin"
}
Payload
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022,
  "exp": 1900000000
}

export JWT=<TOKEN> // from above
curl -i -H "Authorization: Bearer ${JWT}" $PROXY_IP/get  // it works and validated
```