
# RBAC

*Creating Multiple users with Fine grained access to resources and Permissons ins k8s*

 
This Guide is for RBAC in kubernetes

---
## Creating a User & a Context

-  ### Generate an RSA key using openssl

		openssl genrsa -out mayur.key 2048
    
-  ### Create the certificate signing request file, mayur.csr
		openssl req -new -key mayur.key -out mayur.csr -subj "/CN=mayur/O=devs/O=verti.org"

> The Certificates of Signing Authorities of K8s will be stored in `/etc/kubernetes/pki`
> We are signing this certificate using the certificates of the "Kubernetes Certificate signing authority"

-  ### Creating and Signing the certfiicate, mayur.crt
		sudo openssl x509 -req -in mayur.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out mayur.crt -days 500

-  ### Make a directory, $HOME/.certs and move the files
		mkdir -p $HOME/.certs && mv *.crt *.key $HOME/.certs
		
		cd $HOME/.certs
		
-  ### Set the Credentials in Kube Config
		 
		kubectl config set-credentials mayur --client-certificate=$HOME/.certs/mayur.crt --client-key=$HOME/.certs/mayur.key --embed-certs=true
		
-  ### Create the Context with user 

		kubectl config set-context mayur-kube --cluster=kubernetes --user=mayur --namespace=default
	`kubectl config view`
	`kubectl config get-contexts`
	`kubectl config use-context mayur-kube`


resources are in the same folder
-  ### Create a Role & Apply

		kubectl apply -f read-role.yaml

-  ### Create a Role Binding to Attach & Apply
		kubectl apply -f read-role-binding.yaml
