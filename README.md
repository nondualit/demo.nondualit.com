# nondualit.com
Now running on S3 using Cloudfront and Codecommit/build

######################################################################
For k8s 

Template for the website Nondual IT
https://nondualit.com

This website is based on a free Creative Tim template https://www.creative-tim.com

You can use this repo for your own and change it and make it yours.

Use it in Kubernetes with nginx

helm repo add bitnami https://charts.bitnami.com/bitnami

helm install nondualit-nginx --set cloneStaticSiteFromGit.enabled=true --set cloneStaticSiteFromGit.repository=https://git.nondualit.com/repo/nondualit-com.git --set cloneStaticSiteFromGit.branch=master bitnami/nginx

More info

https://github.com/bitnami/charts/tree/master/bitnami/nginx/#installing-the-chart


All pictures made by Anibal Ojeda can be used under the GNU General Public License (GPL) 3.0

k8s rulez
