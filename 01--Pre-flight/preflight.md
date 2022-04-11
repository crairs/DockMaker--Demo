## <font color='red'>Preflight - Hardware & Utils</font>  

The following pre-requisites configure the Pentaho Server and Data Integration 9.3.

Prerequisites for the Pentaho Server 9.3 machine:
* Docker 
* Docker Compose
* Harbor

---

<em>Install Docker, Docker-Compose & Harbor</em>  

The Harbor community has provided a script that with a single command prepares an Ubuntu 20.04 machine for Harbor and deploys the latest stable version.  
This script installs Harbor with an HTTP connection, Clair, and the Chart Repository Service. It does not install Notary, which requires HTTPS.  

``run the script:``
```
sudo ./harbor.sh
```
Select whether to deploy Harbor using the IP address or FQDN of the host machine.

This is the address at which you access the Harbor interface and the registry service.

* IP address, enter 1.  
* FQDN, enter 2.  

``enter option 2:``  

When the script reports Harbor Installation Complete, log in to your new Harbor instance.

  > browse to: http://harbor.skytap.example

User name: admin  
Password: Harbor12345  

After deployment, you can enable HTTPS and Notary by reconfiguring the installation. 

  > for further details: https://goharbor.io/docs/2.0.0/install-config/configure-https/

--- 

<em>Push & Pull Images to/from Harbor</em>

Once logged in, you should be able to create new projects, pull and push images into Harbor. 

``log in to Harbor with CLI:``
```
cd harbor
docker login harbor.skytap.example
Username: admin
Password: Harbor12345
```
Harbor optionally supports HTTP connections, however the Docker client always attempts to connect to registries by first using HTTPS. If Harbor is configured for HTTP, you must configure your Docker client so that it can connect to insecure registries. In your Docker client is not configured for insecure registries, you will see the following error when you attempt to pull or push images to Harbor:  

```Error response from daemon: Get https://myregistrydomain.com/v1/users/: dial tcp myregistrydomain.com:443 getsockopt: connection refused.```

Resolution: 
* Ensure the /etc/docker/daemon.json has the IP or FQDN. 
* Ensure all the containers have started. Check containers in Docker section of VSC.

```
{
"insecure-registries" : ["myregistrydomain.com:port", "0.0.0.0"]
}
```

``create a new project:``
```
in the UI create a project called 'busybox`
```
Switch back to Terminal.

``pull the image:``
```
docker pull busybox
```
``list the images:``
```
docker images
```
``hello world in container:``
```
docker run busybox echo "hello from busybox"
```
``tag the image:``
```
docker tag busybox:latest harbor.skytap.example/busybox:latest
```
``push to harbor:``
```
docker push harbor.skytap.example/busybox/busybox:latest
```
* Log back into Harbor -> Projects -> Busybox .. 

Let's now see if the image can be pulled.

``remove busybox/busybox:latest container:``
```
docker image rm harbor.skytap.example/busybox/busybox:latest
```
or
use the Harbor UI..
``pull image from Harbor:``
```
docker pull harbor.skytap.example/busybox/busybox
```
Note: it will pull the latest

---

<em>Configure HTTPS connection</em>

<font color='teal'>This section is for reference only.</font>

In a production environment, you should obtain a certificate from a CA. In a test or development environment, you can generate your own CA. To generate a CA certficate, run the following commands.

``generate a CA certificate private key:``
```
openssl genrsa -out ca.key 4096
```
``enter cert details:``
```
#Syntax to generate cert and key: 
openssl req \
    -newkey rsa:4096 -nodes -sha256 -keyout domain.key \
    -x509 -days 365 -out domain.crt \
    -subj "/CN=localhost/C=<Country>/ST=<State>/L=<Location>/O=<Organization>"
```
```
openssl req -newkey rsa:4096 -nodes -sha256 -keyout harbor.skytap.example.key -x509 -days 365 -out harbor.skytap.example.crt -subj "/CN=harbor.skytap.example/C=UK/ST=London/L=London/O=HitachiVantara"
```
``configure the Harbor Installer - harbor.yml:``
hostname — set this to either the IP address or the domain of your hosting server.
harbor_admin_password — set this to a strong, unique password.

``edit the paths of the keys to reflect as shown in below example:``
```
hostname: harbor.skytap.example
# http related config
http:
# port for http, default is 80. If https enabled, this port will redirect to https port
port: 80
# https related config
https:
# https port for harbor, default is 443
port: 443
# The path of cert and key files for nginx
certificate: /etc/ssl/harbor.skytap.example.crt
private_key: /etc/ssl/harbor.skytap.example.key
```


---
