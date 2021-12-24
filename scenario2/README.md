# Steps

## Nginx reverse proxy
```bash
docker build -t benhpoh/reverseproxy .
docker push benhpoh/reverseproxy
```
Dockerfile simply uses a standard NGINX image, and adds the `nginx.conf` settings to proxy requests through to google.com

## Kubernetes deployment
```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

Creates a 3 replica deployment running the custom nginx image created above.

Service will forward traffic on `localhost:30100` to the reverseproxy pod, which redirects to google.com