# Purpose of This Repository
The purpose of this repository is to learn Kubernetes (K8s). This manifest shares the source A source code for my website, nondualit.com, so you can experiment with setting it up on your own Kubernetes cluster. For example, you can use DigitalOcean or TransIP K8s Stack, which are among the most affordable options available online.

What You’ll Need:
- An account with DigitalOcean or TransIP K8s Stack
- A valid domain
- A working email address
- At least $24/month for a 2-node cluster

To-Do:
- Add the token as a secret
- Make the repository private

Feel free to replace my website with your own code if you prefer.

Hace fun and use it if you want!
k8s rulez

#Run this manifest for example on DigitalOcean k8s

<pre>
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      volumes:
        - name: site-content
          emptyDir: {}
      initContainers:
        - name: git-clone-repository
          image: bitnami/git:latest
          command:
            - /bin/bash
            - '-ec'
            - >
              [[ -f "/opt/bitnami/scripts/git/entrypoint.sh" ]] && source "/opt/bitnami/scripts/git/entrypoint.sh";
              git clone https://github.com/nondualit/nondualit.com.git --branch master /app
          volumeMounts:
            - name: site-content
              mountPath: /app
      containers:
        - name: git-repo-syncer
          image: bitnami/git:latest
          command:
            - /bin/bash
            - '-ec'
            - >
              [[ -f "/opt/bitnami/scripts/git/entrypoint.sh" ]] && source "/opt/bitnami/scripts/git/entrypoint.sh";
              while true; do
                  cd /app && git pull origin master;
                  sleep 60;
              done
          volumeMounts:
            - name: site-content
              mountPath: /app
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: site-content
              mountPath: /usr/share/nginx/html
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  annotations:
    service.beta.kubernetes.io/do-loadbalancer-size-slug: "lb-small"
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/cluster-issuer: "letsencrypt"
spec:
  tls:
  - hosts:
    - YOURDOMAIN
    secretName: DOMAIN-tls
  rules:
  - host: YOURDOMAIN
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
---
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    email: MAIL  # Ensure this email is correct
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-private-key
    solvers:
    - http01:
        ingress:
          class: nginx
 </pre>

#To get your ingress IP (after manifest)
<pre>kubectl get ingress nginx-ingress</pre>
