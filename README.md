A namespace has to be created before deploying the app: kubectl create namespace stocks-app

A service account then has to be created for the namespace
 - kubectl -n stocks-app create serviceaccount jenkins
 - kubectl -n stocks-app create rolebinding jenkins-binding --clusterrole=cluster-admin --serviceaccount=stocks-app:jenkins
 - kubectl create token jenkins -n stocks-app
With the output from this command create a secret text credential in Jenkins

A NFS server is needed for the DB volume. In this case the server will be lnx1. The server needs to be able to solve the client IPs to names via DNS or hosts file.
Server config:
 - sudo apt install nfs-kernel-server
 - sudo systemctl start nfs-kernel-server.service
 - sudo systemctl enable nfs-kernel-server.service
 - sudo mkdir /srv/stocks_nfs
 - sudo nano /etc/exports [Add: /srv/stocks_nfs    *(rw,sync,no_subtree_check,no_root_squash)]
 - sudo exportfs -a

On the kubernetes cluster create the PV:
 - kubectl apply -f database-pv.yaml

Create a secret with the DB username and password
 - kubectl create secret generic db-creds --from-literal=stocksmongouser=user --from-literal=stocksmongopassword=pass --namespace stocks-app

 Create a secret for the SSL certificate
  - Create a server.key file with the private key
  - Create a server.crt file for the public key
  - Create secret: kubectl create secret generic ssl-cert --from-file=tls.crt=./server.crt --from-file=tls.key=./server.key --namespace stocks-app

  Create a secret for the jwt
   - kubectl create secret generic jwt-secret --from-literal=jwtsecret=randomtext --namespace stocks-app
