# Hyper Protect Virtual Servers (HPVS) Secure Build process overview

## Prerequisites

See the [`Prerequisites` page](../prerequisites.md){target=_blank} to complete the prerequisites for this trial. You must complete the prerequisites listed if you want to use your own image. This includes creating accounts and credentials to access the Source Code Management (SCM) [GitHub](https://github.com){target=_blank} and the Docker Image Management (Docker Registry) [Docker Hub](https://hub.docker.com/){target=_blank} for the lab. For more information about creating the certificate and key to securely communicate with Secure Build Server, see [Create your Secure Build Server](create-server.md){target=_blank}.

## The Big Picture

There are 5 steps of the Secure Build process:

1. [SETUP](#setup)
2. [ENABLEPORTS](#enableports)
2. [BUILD](#build)
3. [REGISTER](#register)
4. [DEPLOY](#deploy)
5. [ACCESS](#access)
6. [CLEAN UP](#cleanup)  
   These points are explained in more detail in their respective sections below.

### SETUP

Configure your environment with the necessary credentials to connect to [GitHub](https://github.com){target=_blank} and [Docker Hub](https://hub.docker.com/){target=_blank} respectively. Then you must create the certificate and key to securely communicate with the Secure Build Server (for more information see [`Create your Secure Build Server`](create-server.md){target=_blank}).

### ENABLEPORTS

Before you build a docker image by using the Hyper Protect base images, you must open the required ports for your application. For more information, see [`Enabling ports`](sbs-ports-setup.md){target=_blank}.

### BUILD

Securely build a Docker Image. See [`Create your HPVS Secure Build Server`](create-server.md){target=_blank}, or [`Create your HPVS Secure Build Server by using a config file`](create-server-hpvsdeploy.md){target=_blank}, and [`Securely Build your Application`](build.md){target=_blank}.

The Secure Build Process leverages a **Hyper Protect Virtual Server**, called the Secure Build Server, to establish trust and security in the [Docker Content Trust](https://docs.docker.com/engine/security/trust/content_trust/){target=_blank} process. This ensure images verified using the [Notary service architecture](https://docs.docker.com/notary/service_architecture/){target=_blank} were safely built and signed. This is done by generating the repository and root keys used to establish trust with the [Notary Service](https://docs.docker.com/notary/service_architecture/){target=_blank} inside of the Secure Build Server to guarantee that the initial keys used for a repository are trusted on first use (solving the [TOFU](https://en.wikipedia.org/wiki/Trust_on_first_use){target=_blank} problem).

The table details The full list of the keys used during the Secure Build lifecycle.

| Key Name  | Key Function | Private Key Location | How Created | Owned by Whom |
|---|---|---|---|---|
| Image Signing Key | Pushing Docker images to a Docker repository | Encrypted volume on the Secure Build container | Created by the remote registry server on first push to the remote repository, and written to Secure Build container |  ISV or application developer  |
| Manifest Signing Key | Signing a manifest created by Secure Build | Encrypted volume on the Secure Build container | Created by the Secure Build container when an image is built |  ISV or application developer |
| Client certificate and Key | Used by the cloud administrator to securely interact with the Secure Build REST API, contains certificate and private key | Client | Created on creation of the Secure Build container and provided to the client as the file specified in their CLIENT_CRT_KEY setting | Cloud administrator |


### REGISTER

Register your securely built Docker image with your Secure Service Container to verify its validity. See [`Generating the signing keys`](../byoi/gen_sign_key.md){target=_blank}, and [`Deploy your Securely Built Application as a Hyper Protect Virtual Server`](deploy-app.md){target=_blank}.


### DEPLOY

Deploy your application `(from the verified repository)` as a Hyper Protect Virtual Server within its own secure environment (see [`Deploy your Securely Built Application as a Hyper Protect Virtual Server`](deploy-app.md){target=_blank}). The hosted trial setup that is available allows you to experience the digital banking application. If you want to try any other application, follow the instruction at [`Building your application with the Secure Build virtual server`](https://www.ibm.com/support/knowledgecenter/SSHPMH_1.2.x/topics/build_app_withsb.html){target=_blank}.

### ACCESS

Access your application with security built-in. See [`Welcome to the Digital Banking Application`](digital_banking.md){target=_blank}.

### CLEANUP

Clean up your environment for future users. See [`Clean up your environment`](cleanup.md){target=_blank}.
