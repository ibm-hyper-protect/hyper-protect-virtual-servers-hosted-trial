# Deploy your Securely Built Application as a Hyper Protect Virtual Server

The example application used to showcase the Secure Build virtual server is the digital banking application, that requires two Secure Build virtual servers, one for building the MongoDB application and the second one for the digital banking application. Two virtual servers are created for the images, one for MongoDB, and one for the digital banking application. The following steps are required to deploy this application:  

1. Create a Secure Build virtual server for building the MongoDB image. See the section: "Procedure to create a Secure Build virtual server (for building the MongoDB image" in the topic [`Create your HPVS Secure Build Server`](create-server.md){target=_blank}, or [`Create your HPVS Secure Build Server by using a configuration file`](create-server-hpvsdeploy.md){target=_blank}.

2. Build the MongoDB image by using the Secure Build process. See the section: "Procedure to create the MongoDB image" in the topic [`Securely Build your Application`](build.md){target=_blank}.

3. Create a MongoDB virtual server. This server must be up and running before you create the digital banking virtual server. See the section "Procedure to create the MongoDB virtual server" in the topic [`Create your HPVS Secure Build Server by using a configuration file`](create-server-hpvsdeploy.md){target=_blank}.

4. Create a Secure Build virtual server for building the digital application image. See the section: "Procedure to create a Secure Build virtual server (for building the digital banking application image)" in the topic [`Create your HPVS Secure Build Server by using a configuration file`](create-server-hpvsdeploy.md){target=_blank}.

5. Build the digital banking image by using the Secure Build process. See the section: "Procedure to create the digital banking image" in the topic [`Securely Build your Application`](build.md){target=_blank}.

6. Create a Secure Build virtual server for deploying the digital application image. See the section:  Procedure to create the digital virtual server" in the topic [`Create your HPVS Secure Build Server by using a configuration file`](create-server-hpvsdeploy.md){target=_blank}.

When you complete the steps listed above, the application is deployed and you can access the digital banking application. See [`Welcome to the Digital Banking Application`](digital_banking.md){target=_blank} for details.
