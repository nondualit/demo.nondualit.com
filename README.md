# nondualit.com template
use it if you want!

# k8s rulez

Run this manifest for example on DigitalOcean k8s 

To get your ingress IP (after manifest)
<pre>kubectl get ingress nginx-ingress</pre>

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
