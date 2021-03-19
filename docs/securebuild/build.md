# Securely Build your Application

You will use the Secure Build process to build the two virtual servers that are required for the digital banking application. This topic provides the details for building the Secure Build virtual servers.

## Prerequisites

- Ensure that you have all the user IDs and passwords to pull the base images, push the built images, and pull the built images from the remote Docker registry server.
- Before you build a docker image by using the Hyper Protect base images, you must open the required ports for your application. For more information, see [`Enabling ports`](sbs-ports-setup.md){target=_blank}.

## Procedure to create the MongoDB image

On your x86 or Linux on IBM Z/LinuxONE (i.e., s390x architecture) management server, complete the following steps with root user authority.

1. To generate the signing keys, follow the instructions listed in the topic [`Generating the signing keys`](../byoi/gen_sign_key.md){target=_blank}.

2. Create the configuration `mongo_secure_build.yml` file for MongoDB. You can use the `$HOME/hpvs/config/securebuild/secure_build.yml.example` example file as a reference when updating the file.


    ???+ example "mongo_secure_build.yml"

        ```
        secure_build_workers:
        sbs:
           url: 'https://129.40.15.77>'
           port: '21443'
           cert_path: '/home/hpvs_user/hpvs/config/securebuild/keys/sbs_cert>'
           key_path: '/home/hpvs_user/hpvs/config/securebuild/keys/sbs_key>'
        regfile:
           id: 'securebuildcontainer472'
        github:
           url: 'git@github.ibm.com:sscicpci/SolutionTest_HPVSonPrem_TestApp.git>'
           branch: 'mongo_sbs'
           ssh_private_key_path: '/home/hpvs_user/.ssh/id_rsa>'
           recurse_submodules: 'False'
           dockerfile_path: './Dockerfile'
        docker:
           push_server: 'docker_reg'
           base_server: 'docker_reg'
           pull_server: 'docker_reg'
           repo: 'hpvsdemo/mongo_demo'
           image_tag_prefix: 'latest'
           content_trust_base: 'True'
        manifest_cos:
           bucket_name: ''
           api_key: ''
           resource_crn: ''
           auth_endpoint: ''
           endpoint: ''
        env:
           whitelist: ["ROOT_SSH_KEY","SERVER_KEY", "RUNQ_ROOTDISK", "LOGTARGET", "ROOTFS_LOCK"]
        # build:
        #   args:
        signing_key:
           private_key_path: '/home/hpvs_user/.ssh/hpvs_user/isv_user.private'
           public_key_path: '/home/hpvs_user/.ssh/hpvs_user/isv_user.pub'
        ```

3. The application required to build the MongoDB application is available in the GitHub repository (details of repository is specified in the `github` parameters of the configuration file shown in the previous step) by choosing one of the following options:  

    - Use one command to perform all the Secure Build actions including initialization, build, and generating the encrypted repository registration file. This option is recommended if you are building the application by using the Secure Build for the first time. The passphrase used is "over-the-lazy-dog".
    ```
    hpvs sb init --config $HOME/hpvs/config/mongo_secure_build.yml --out $HOME/hpvs/config/securebuild/regfiles/encryptedRepoRegistration_mongo.enc --build
    ```
    - Use individual commands to perform each step of building the application by using the Secure Build virtual server. This option is recommended if you plan to build the application by using the Secure Build multiple times. In this scenario, you can run the `hpvs sb build` command for subsequent builds.
    ```    
    hpvs sb build --config $HOME/hpvs/config/securebuild/mongo_secure_build.yml
    hpvs sb regfile --config $HOME/hpvs/config/securebuild/mongo_secure_build.yml --out $HOME/hpvs/config/securebuild/regfiles/encryptedRepoRegistration_mongo.enc
    ```

   You can use the  `hpvs sb status` command to view the progress of the secure build.

???+ example "Example Output"
    ![Create mongo](securebuild-Images/mongo.png)



After the Secure build process completes, the MongoDB image is pushed to the DockerHub repository, from where it will be later pulled to create the MongoDB virtual server.


## Procedure to create the digital banking image

You will follow the same steps as mentioned in the section above but use a configuration file that specifies the details that are required for the MongoDB virtual server.

1. Create the configuration `digital_secure_build.yml` file for the digital application. You can use the `$HOME/hpvs/config/securebuild/secure_build.yml.example` example file as a reference when updating the file.


    ???+ example "digital_secure_build.yml"

        ```
        secure_build_workers:
        sbs:
           url: 'https://129.40.15.77>'
           port: '21443'
           cert_path: '/home/hpvs_user/hpvs/config/securebuild/keys/sbs_cert>'
           key_path: '/home/hpvs_user/hpvs/config/securebuild/keys/sbs_key>'
        regfile:
           id: 'digital'
        github:
           url: 'git@github.ibm.com:sscicpci/SolutionTest_HPVSonPrem_TestApp.git>'
           branch: 'digitalbank_https_sbs'
           ssh_private_key_path: '/home/hpvs_user/.ssh/id_rsa>'
           recurse_submodules: 'False'
           dockerfile_path: './Dockerfile_with_ARG'
        docker:
           # I am using the same registry for all 3 users but you can use 3 different registries. Use <hpvs registry list> command to view  the registries    
           push_server: 'docker_reg'
           base_server: 'docker_reg'
           pull_server: 'docker_reg'
           repo: 'hpvsdemo/digitaldemo'
           image_tag_prefix: 'latest'
           content_trust_base: 'True'
        manifest_cos:
           bucket_name: ''
           api_key: ''
           resource_crn: ''
           auth_endpoint: ''
           endpoint: ''
        env:
           whitelist: ["ROOT_SSH_KEY","SERVER_KEY", "RUNQ_ROOTDISK", "LOGTARGET", "ROOTFS_LOCK"]
        build:
          args:
            MONGO_URL: 'mongodb://test:Passw0rd@129.40.15.19:2701/test'
        signing_key:
           private_key_path: '/home/hpvs_user/.ssh/hpvs_user/isv_user.private'
           public_key_path: '/home/hpvs_user/.ssh/hpvs_user/isv_user.pub'
        ```

3. The application required to build the digital banking application is available in the GitHub repository (details of repository is specified in the `github` parameters of the configuration file shown in the previous step) by choosing one of the following options:  

    - Use one command to perform all the Secure Build actions including initialization, build, and generating the encrypted repository registration file. This option is recommended if you are building the application by using the Secure Build for the first time. The passphrase used is "over-the-lazy-dog".
    ```
    hpvs sb init --config $HOME/hpvs/config/securebuild/digital_secure_build.yml --out $HOME/hpvs/config/securebuild/regfiles/encryptedRepoRegistration_digital.enc --build
    ```
    - Use individual commands to perform each step of building the application by using the Secure Build virtual server. This option is recommended if you plan to build the application by using the Secure Build multiple times. In this scenario, you can run the `hpvs sb build` command for subsequent builds.
    ```    
    hpvs sb build --config $HOME/hpvs/config/securebuiild/digital_secure_build.yml
    hpvs sb regfile --config $HOME/hpvs/config/securebuild/digital_secure_build.yml --out $HOME/hpvs/config/securebuild/regfiles/encryptedRepoRegistration_digital.enc
    ```

   You can use the  `hpvs sb status` command to view the progress of the secure build.

???+ example "Example Output"
    ![Create digital](securebuild-Images/digital.png)



!!! note
    - If the `hpvs sb init`, `hpvs sb build`, or the `hpvs sb regfile` commands fails for any reason, for example you specified incorrect parameters, then you can use the `hpvs sb update` command to update the configuration of the Secure Build configuration and rerun the commands with the updated configuration. The `regfile[id]` and `docker[repo]` parameters cannot be updated by using this command.
    - You can use the `hpvs sb log` command to view the run time logs of the secure build process, or for troubleshooting or debugging. - The logs are available when you run the `hpvs sb init`, `hpvs sb build`, or the `hpvs sb regfile` commands.
    - You can use the `hpvs sb status` command to view the status of the last secure build process.
    - You can use the `hpvs sb clean` command to clean the logs of the secure build process. Build artifacts from the earlier builds are  deleted.
