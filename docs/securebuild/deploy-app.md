# Deploy your Securely Built Application as a Hyper Protect Virtual Server

The example application used to showcase the Secure Build virtual server is the digital banking application, that requires two Secure Build virtual servers, one for building the MongoDB application and the second one for building the digital banking application. The two application images are deployed in two virtual servers.


## Procedure to deploy the banking application
Follow the instructions provided here to deploy the banking application:  

1. To create a Secure Build virtual server for building the MongoDB image. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#procedure-to-create-a-secure-build-virtual-server-for-building-the-mongodb-image){target=_blank} from the topic [`Creating the Secure Build Server`](create-server-hpvsdeploy.md){target=_blank}.

2. Build the MongoDB image by using the Secure Build process. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/build/#procedure-to-create-the-mongodb-image){target=_blank} from the topic [`Securely Build your application`](build.md){target=_blank}.

3. Create a MongoDB virtual server. This server must be up and running before you create the digital banking virtual server. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#procedure-to-create-the-mongodb-virtual-server){target=_blank} from the topic [`Creating the Secure Build Server`](create-server-hpvsdeploy.md){target=_blank}.

4. Create a Secure Build virtual server for building the digital application image. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#procedure-to-create-a-secure-build-virtual-server-for-building-the-digital-banking-application-image){target=_blank} from the topic [`Creating the Secure Build Server`](create-server-hpvsdeploy.md){target=_blank}.

5. Build the digital banking image by using the Secure Build process. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/build/#procedure-to-create-the-digital-banking-image){target=_blank} from the topic [`Securely Build your application`](build.md){target=_blank}.

6. Create a Secure Build virtual server for deploying the digital application image. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#procedure-to-create-the-digital-virtual-server){target=_blank} from the topic [`Creating the Secure Build Server`](create-server-hpvsdeploy.md){target=_blank}.

When you complete the steps listed above, the application is deployed and you can access the digital banking application. See [`Welcome to the Digital Banking Application`](digital_banking.md){target=_blank} for details.
