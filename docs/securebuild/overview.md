# Secure Build overview

## Prerequisites

See the [`Prerequisites` page](../prerequisites.md){target=_blank} to complete the prerequisites for this trial if you want to use your own application. This includes creating accounts and credentials to access the Source Code Management (SCM) [GitHub](https://github.com){target=_blank} and the Docker Image Management (Docker Registry) [Docker Hub](https://hub.docker.com/){target=_blank}. For more information about creating the certificate and key to securely communicate with Secure Build Server, see [`Creating the Secure Build Server`](create-server-hpvsdeploy.md){target=_blank}.

## The Big Picture

There are the steps of the Secure Build process:

1. [SETUP](#setup)
2. [BUILD](#build)
3. [DEPLOY](#deploy)
4. [ACCESS](#access)
5. [CLEAN UP](#cleanup)  
   These points are explained in more detail in their respective sections below. The steps detailed here help you deploy the digital banking application that is provided as an example as a part of the hosted trial environment. To deploy your own application, complete the prerequisites ([`Prerequisites` page](../prerequisites.md){target=_blank}), then refer the IBM Knowledge Center topic: [`Building your application with the Secure Build virtual server`]https://www.ibm.com/docs/en/hpvs/1.2.x?topic=later-building-your-application-secure-build-virtual-server){target=_blank} for detailed instructions.

### SETUP

Complete the following steps:
- Configure your environment with the necessary credentials to connect to [GitHub](https://github.com){target=_blank} and [Docker Hub](https://hub.docker.com/){target=_blank} respectively.  
- Create the certificate and key to securely communicate with the Secure Build Server (for more information, see [`Creating the Secure Build Server`](create-server-hpvsdeploy.md){target=_blank}).


### BUILD

Complete the following steps:
- Create the Secure Build virtual servers for building the MongoDB and the digital banking application Docker images (for more information, see
[`Creating the Secure Build Server`](create-server-hpvsdeploy.md){target=_blank}.  
- Build the MongoDB and the digital banking application Docker images (for more information, see)[`Securely Build your application`](build.md){target=_blank}).  

The Secure Build Process leverages a **Hyper Protect Virtual Server**, called the Secure Build Server, to establish trust and security in the [Docker Content Trust](https://docs.docker.com/engine/security/trust/content_trust/){target=_blank} process. This ensure images verified using the [Notary service architecture](https://docs.docker.com/notary/service_architecture/){target=_blank} were safely built and signed. This is done by generating the repository and root keys used to establish trust with the [Notary Service](https://docs.docker.com/notary/service_architecture/){target=_blank} inside of the Secure Build Server to guarantee that the initial keys used for a repository are trusted on first use (solving the [TOFU](https://en.wikipedia.org/wiki/Trust_on_first_use){target=_blank} problem).

The table details The full list of the keys used during the Secure Build lifecycle.

| Key Name  | Key Function | Private Key Location | How Created | Owned by Whom |
|---|---|---|---|---|
| Image Signing Key | Pushing Docker images to a Docker repository | Encrypted volume on the Secure Build container | Created by the remote registry server on first push to the remote repository, and written to Secure Build container |  ISV or application developer  |
| Manifest Signing Key | Signing a manifest created by Secure Build | Encrypted volume on the Secure Build container | Created by the Secure Build container when an image is built |  ISV or application developer |
| Client certificate and Key | Used by the cloud administrator to securely interact with the Secure Build REST API, contains certificate and private key | Client | Created on creation of the Secure Build container and provided to the client as the file specified in their CLIENT_CRT_KEY setting | Cloud administrator |


### DEPLOY

Deploy your application `(from the verified repository)` as a Hyper Protect Virtual Server within its own secure environment (for more information, see [`Deploy your application`](deploy-app.md){target=_blank}).

### ACCESS

Access your application with security built-in. See [`Welcome to the Digital Banking Application`](digital_banking.md){target=_blank}.

### CLEANUP

Clean up your environment for future users. See [`Clean up your environment`](cleanup.md){target=_blank}.
