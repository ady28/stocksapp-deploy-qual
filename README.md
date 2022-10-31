A namespace has to be created before deploying the app: kubectl create namespace stocks-app

A service account then has to be created for the namespace
 - kubectl -n stocks-app create serviceaccount jenkins
 - kubectl -n stocks-app create rolebinding jenkins-binding --clusterrole=cluster-admin --serviceaccount=stocks-app:jenkins
 - kubectl -n stocks-app get serviceaccount jenkins -o go-template --template='{{range .secrets}}{{.name}}{{"\n"}}{{end}}'
With the output of the last command run the following:
 - kubectl -n stocks-app get secrets [output-text] -o go-template --template '{{index .data "token"}}' | base64 -d
With the output from this command create a secret text credential in Jenkins

A NFS server is needed for the DB volume. In this case the server will be lnx1. The server needs to be able to solve the client IPs to names via DNS or hosts file.
Server config:
 - sudo apt install nfs-kernel-server
 - sudo systemctl start nfs-kernel-server.service
 - sudo systemctl enable nfs-kernel-server.service
 - sudo mkdir /srv/stocks_nfs
 - sudo nano /etc/exports [Add: /srv/stocks_nfs    *(rw,sync,no_subtree_check)]
 - sudo exportfs -a

On the kubernetes cluster create the PV:
 - kubectl apply -f database-pv.yaml

Create a secret with the DB username and password
 - kubectl create secret generic db-creds --from-literal=stocksmongouser=user --from-literal=stocksmongopassword='pass' --namespace stocks-app