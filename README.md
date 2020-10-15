**WORKSHOP: Add domain names over https to your Kubernetes application**

[Lee Bowie](https://www.linkedin.com/in/lee-bowie/), IBM Technology Evangelist, main presenter

[Fraser Anderson](https://www.linkedin.com/in/fraser-anderson-ottawa/), IBM Global Elite, documentation

**Presentation:**

The associated presentation is available as [Google Slides](https://ibm.biz/itest-mk8s).

**Prerequisites:**

The following applications must be installed:

* [Minikube](https://minikube.sigs.k8s.io/docs/) 
* [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
* [openssl](https://wiki.openssl.org/index.php/Binaries)
* [virtualbox](https://www.virtualbox.org/wiki/Downloads)

After installing the prerequisites, extract the workshop files to a folder on your computer.

**Steps:**

 1. Open a Command Prompt and navigate to the folder containing the extracted files.
 2. Start Minikube with the command \
    \
    minikube start
 3. Enable the Ingress addon with the command\
    \
    minikube addons enable ingress
 4. Set your Docker environment to point to the Minikube Docker. Enter the command\
    \
    minikube docker-env
 5. After the command has run, instructions are given to point your minikube's docker daemon, for example\
    \
    eval $(minikube -p minikube docer-env)

    Copy these instructions, paste them into your command line and run.
 6. The next step is to define a namespace. The namescapce contains the API pod, the Database pod and the App pod. Enter the command\
    \
    kubectl apply -f itest -namespace.yaml

    A message appears stating that the itest-namespace is created
 7. Now, grab an image for our deployment. Let's grab Apache Web Server. Run the command

    docker pull httpd:2.4.46
 8. Tag the deployment with the command \
    \
    docker tag httpd:2.4.46 itest-image:v1
 9. Create the deployment, enter \
    \
    kubectl apply -f itest-deployment.yaml

    A message appears stating that the deployment has been created. To understand what is being created open the file *itest-deployment* in your favourite text editor. The deployment is created in the namespace created in **Step 6** called *itest-namespace*. The deployment contains two replicas of the pod to be created. The application is given the name *itest-app* and includes the *itest-container* with the image created in **Step 7** and **Step 8** named *itest-image:v1*.
10. Create a service to expose the deployment, enter \
    \
    kubectl apply -f itest-service.yaml

    A message appears stating that the service has been created. Open the file *itest-service.yaml* in your favourite text editor to see the port assignments. In this case the external port is 30005 and the internal port is the standard port 80.
11. Run the command \
    \
    kubectl get all --namespace itest-namepace \
    \
    to see the pods deployed. Note that there are two pods running as requested in **Step 9**.
12. Now it's time to secure our environment. Run the command \
    \
    openssl req -newkey rsa:2048 -x509 -sha256 -days 3650 -nodes -out tls.crt -keyout tls.key | base64 -w0^C
13.  When prompted, enter the Country Name, State or Province Name, City, Organization Name, Organizational Unit Name, Common Name and Email address. Two files are created, *tls.crt* and *tls.key*.
14. In your text editor, open three files - *tls.crt*, *tls.key* and *itest-secret.yaml*. Copy and paste the hash contents of the tls.crt file to the appropriate location in the *itest-secret.yaml* file. Repeat for the hash contents of the *tls.key* file. Save the *itest-secret.yaml* file.
15. Run the command \
    \
    kubectl apply -f itest-secret.yaml

    A message appears stating that *itest-secret-tls* is created.
16. Run the command\
    \
    kubectl apply -f itest-ingress.yaml

    A message appears stating that *itest-ingress* is created. Open the file *itest-ingress.yaml* to understand the Ingress deployment. The host name is itest.net for the outside world. Because we're using a secure connection the secret name, itest-secret-tls, is included. The secret includes the required certificate. The file goes on to direct all traffic to the the itest-service on port 30005.
17. Run the command\
    \
    kubectl get ingress --namespace itest-namespace
18. Record the IP address for Ingress, for example 192.168.99.141.
19. Open the hosts file for your computer. For our example, add the following line

    192\.168.99.141 itest.net

    All requests for itest.net are now directed to 192.168.99.141. For instructions on editing a hosts file on Microsoft Windows, [click here](https://www.groovypost.com/howto/edit-hosts-file-windows-10/).
20. Save the hosts file.
21. Run the command 

    ping itest.net 

    to ensure that traffic is now going to 192.168.99.141.
22. Open a web browser. Enter itest.net in your web browser's address bar. You receive warnings that the connection is not certificate. This is due to the fact that the certificate is self-signed. You can safely ignore these messages.

    You see a message - It works!