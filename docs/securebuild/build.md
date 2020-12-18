# Securely Build your Application


## Prerequisites

- Ensure that you have all the user IDs and passwords to pull the base images, push the built images, and pull the built images from the remote Docker registry server.
- When you are using IBM Hyper Protect Virtual Servers version 1.2.2, or later, before you build a docker image by using the Hyper Protect base images, you must open the required ports for your application. For more information, see [`Enabling ports`](sbs-ports-setup.md){target=_blank}.

## Procedure

On your x86 or Linux on IBM Z/LinuxONE (i.e., s390x architecture) management server, complete the following steps with root user authority.

1. To generate the signing keys, follow the instructions listed in the topic [`Generating the signing keys`](../byoi/gen_sign_key.md){target=_blank}.

2. Create the Secure Build configuration file. You can use the `$HOME/hpvs/config/securebuild/secure_build.yml.example` example file as a reference when updating the file.

   ```
   secure_build_workers:
      sbs:
         url: '<url of the secure build service. e.g- https://10.20.4.67>'
         port: '443'
         cert_path: '<complete path of certificate.  e.g- /root/hpvs/config/securebuild/keys/sbs_cert>'
         key_path: '<complete path of key.  e.g- /root/hpvs/config/securebuild/keys/sbs_key>'
      regfile:
         id: '<Enter Id. It could be any name>'
      github:
         url: '<git hub url. e.g- git@github.com:MyOrg/my-docker-app.git>'
         branch: 'master'
         ssh_private_key_path: '<complete path of key github private key. e.g - /root/git_key>'
         recurse_submodules: 'False'
         dockerfile_path: './Dockerfile'
         docker_build_path: '<Enter the path to the subdirectory within the Github project to be used as the build context for the Docker build>'
      docker:
         push_server: '<get this from hpvs registry list. e.g - docker_push>'
         base_server: '<get this from hpvs registry list. e.g - docker_base>'
         pull_server: '<get this from hpvs registry list. e.g - docker_pull>'
         repo: 'docker_user_name/docker_image_name'
         image_tag_prefix: 'latest'
         content_trust_base: 'True'
      manifest_cos:
         bucket_name: '<Enter the bucket name on the S3 object store where manifest files will be transferred to after each build>'
         api_key: '<Enter the API key used to authenticate with the S3 object store>'
         resource_crn: '<Enter the resource instance ID for the S3 object store>'
         auth_endpoint: '<Enter the authentication endpoint for the S3 object store>' (For example: `https://iam.cloud.ibm.com/identity/token`)
         endpoint: '<Enter the endpoint for the S3 object store>' (For example:'https://s3.us-east.cloud-object-storage.appdomain.cloud')
      # Add all whitelist environment variables that are required in your virtual server. If you try to create a virtual server with environment variables that are not added to the whitelist, then creating the virtual server fails. This is an optional parameter and if you do not have any environment variable for the virtual server, you can comment this parameter.
      env:
         whitelist: [KEY1,KEY2]
      build:
         args:
           <ARG1>: '<value1>'
           <ARG2>: '<value2>'
      signing_key:
         private_key_path: '<Enter the absolute private key path. For example, /root/hpvs/config/securebuild/keys/isv_user.private'
         public_key_path: '<Enter the absolute public key path. For example, /root/hpvs/config/securebuild/keys/isv_user.pub'
   ```

   **Note**:   
   - If the base image in Dockerfile is not signed then the `base_server` parameter is not required and `content_trust_base` must be `False`.
   - It is recommended that you choose an endpoint URL that is located in the same region as your service or application, and specify this URL as the value for the `endpoint` parameter in the `manifest_cos` section of the secure_build.yml file. For more information about identifying endpoint URL, see [`Cloud Object Storage`](https://cloud.ibm.com/docs/cloud-object-storage?topic=cloud-object-storage-endpoints){target=_blank}.

   To configure a Cloud Object Storage service to archive the application manifest files of your applications built by your Secure Build container, ensure that you have the following information about your [IBM Cloud Object Storage](https://cloud.ibm.com/catalog/services/cloud-object-storage) at hand.
    * The API Key to the cloud object storage service
    * The object storage bucket to store the manifest
    * The resource instance name of the cloud object storage service
    * The authentication endpoint for the cloud object storage service
    * The endpoint for the cloud object storage service

3. Build your application and upload the application manifest file to the cloud object storage by using Secure Build. You can choose either of the following options:
  * Use one command to perform all the Secure Build actions including initialization, build, and generating the encrypted repository registration file. This option is recommended if you are building the application by using the Secure Build for the first time.
    ```
    hpvs sb init --config $HOME/hpvs/config/securebuild/secure_build.yml.example --out $HOME/hpvs/config/MyDockerAppImageRegfile.enc --build
    ```
  * Use individual commands to perform each step of building the application by using the Secure Build virtual server. This option is recommended if you plan to build the application by using the Secure Build multiple times. In this scenario, you can run the `hpvs sb build` command for subsequent builds.
    ```    
    hpvs sb build --config $HOME/hpvs/config/securebuild/secure_build.yml.example
    hpvs sb regfile --config $HOME/hpvs/config/securebuild/secure_build.yml.example --out $HOME/hpvs/config/MyDockerAppImageRegfile.enc
    ```
  You can log in to your cloud account and check the application manifest file has been transferred to its bucket in your Cloud Object Storage service after the commands complete execution.

  You can use the `hpvs sb manifest` command to download the manifest file of the secure build.
  ```
  hpvs sb manifest --config $HOME/hpvs/config/securebuild/secure_build.yml.example --name <build_name>
  ```
  where you can get the `<build_name>` by using the `hpvs sb status` after the build completes.
  When the command execution completes, the manifest file is downloaded to the current directory from which the `hpvs sb manifest` command was run from.

  **Note**:
   * If the `hpvs sb init`, `hpvs sb build`, or the `hpvs sb regfile` commands fails for any reason, for example you specified incorrect parameters, then you can use the `hpvs sb update` command to update the configuration of the Secure Build configuration and rerun the commands with the updated configuration. The `regfile[id]` and `docker[repo]` parameters cannot be updated by using this command.
   * You can use the `hpvs sb log` command to view the run time logs of the secure build process, or for troubleshooting or debugging. The logs are available when you run the `hpvs sb init`, `hpvs sb build`, or the `hpvs sb regfile` commands.
   * You can use the `hpvs sb status` command to view the status of the last secure build process.
   * You can use the `hpvs sb clean` command to clean the logs of the secure build process. Build artifacts from the earlier builds are deleted.    
