# nondualit.com

Test website for Nondual IT
https://nondualit-com-tst-4vax3.ondigitalocean.app

Prod website for Nondual IT
https://nondualit.com

eksctl delete cluster --name nondualit-k8s-testcluster

helm install nondualit-app --set cloneHtdocsFromGit.enabled=true --set cloneHtdocsFromGit.repository=https://github.com/nondualit/nondualit.com-test.git  --set cloneHtdocsFromGit.branch=master bitnami/apache

kubectl rollout restart -n default deployment nondualit-app-apache
