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

```bash
curl localhost:30100

# Output:

# <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
# <TITLE>301 Moved</TITLE></HEAD><BODY>
# <H1>301 Moved</H1>
# The document has moved
# <A HREF="http://www.google.com/">here</A>.
# </BODY></HTML>
```
