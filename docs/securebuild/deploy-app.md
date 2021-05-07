# Deploy your Application

The example application used to showcase the Secure Build virtual server is the digital banking application, that requires two Secure Build virtual servers, one for building the MongoDB application and the second one for building the digital banking application. The two application images are deployed in two virtual servers.


## Procedure to deploy the banking application
Follow the instructions provided here to deploy the digital banking application:  

1. Create a Secure Build virtual server for building the MongoDB image. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#creating-a-secure-build-virtual-server){target=_blank} for creating the Secure Build virtual server.

2. Build the MongoDB image by using the Secure Build process. You will use the Secure Build virtual server that you created in step 1, to build the MongoDB image. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/build/#creating-the-mongodb-image){target=_blank} to build the MongoDB application.

3. Create a virtual server to deploy the MongoDB image. This server must be available and in the running state before you create the virtual server that runs the digital banking application. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#creating-the-virtual-server-for-mongodb){target=_blank} to create the virtual server with the MongoDB image.

4. Create a Secure Build virtual server for building the digital banking application image. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#creating-a-secure-build-virtual-server-to-build-the-digital-banking-application){target=_blank} for creating the Secure Build virtual server.

5. Build the digital banking image by using the Secure Build process. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/build/#creating-the-digital-banking-image){target=_blank} to build the digital banking application.

6. Create a virtual server to deploy the digital application image. See these [instructions](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#creating-the-virtual-server-for-the-digital-banking-application){target=_blank} to create the virtual server with the digital banking application image.

When you complete the steps listed above, the application is deployed and you can access the digital banking application. See [`Welcome to the Digital Banking Application`](digital_banking.md){target=_blank} for details.
