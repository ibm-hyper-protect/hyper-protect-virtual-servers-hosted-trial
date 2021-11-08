# Creating the Secure Build Server

You can create the Secure Build virtual server by using the `hpvs deploy` command by specifying a configuration yaml file as an input for the `hpvs deploy` command. This is the recommended option to provision the instance because of it's ease of use, and is also an easier method of creating multiple instances quickly.

You can also use the `hpvs vs create` command to create the virtual server, however this method requires you to enter several configuration information using multiple commands. For more information about this method, see [Create a virtual server by using the hpvs image and hpvs vs create commands](https://www.ibm.com/docs/en/hpvs/1.2.x?topic=later-building-your-application-secure-build-virtual-server#using-hpvs_create){target=_blank}.

## Prerequisites
Before you start the Secure Build process, you must create the certificate and key to securely communicate with Secure Build Server. Complete the following steps.

1. Run the following command.
   ```
   cd $HOME/hpvs/config/securebuild/keys
   ```

2. Create the certificate and key to securely communicate with Secure Build Server.
   ```
   openssl req -newkey rsa:2048 \
   -new -nodes -x509 \
   -days 3650 \
   -out sbs.cert \
   -keyout sbs.key \
   -subj "/C=GB/O=IBM/CN=johndoe.example.com"
   ```

    !!! note

       If you see errors like `random number generator:RAND_load_file:Cannot open file`, then run the following commands.
       ```
       openssl rand -out $HOME/.rnd -hex 256
       ```

3. Run the following command to change the certificate to base64 encoding.
   ```
   echo $(cat sbs.cert | base64) | tr -d ' ' >> sbs_base64.cert
   ```


## Creating a Secure Build virtual server

1. Create the configuration yaml file `securebuild.yml` for the instance by referring to the example file $HOME/hpvs/config/securebuild/vs_securebuild.yml. The `vs_securebuild.yml` has the configuration details for the virtual server and refers to the corresponding sections of the `virtualserver.template.yml` when you run the `hpvs deploy` command. The virtual server template file contains the definitions of the resources, volumes, environment templates, and networks that are required to create a virtual server. For example, the `resourcedefinition: ref` value refers to the `resourcedefinitiontemplate` definition in the template file.    


    ??? example "Example of a Secure Build virtual server configuration file"

        ```
        version: v1
        type: virtualserver
        virtualservers:
        - name: test_securebuild
          host: test2
          repoid: SecureDockerBuild
          imagetag: 1.2.4
          imagefile: /home/hpvs_user/hpvs/config/securebuild/images/SecureDockerBuild.tar.gz
          resourcedefinition:
             ref: small
          environment:
           - key: ROOTFS_LOCK
             value: "y"
           - key: CLIENT_CRT
             value: "@/home/hpvs_user/hpvs/config/securebuild/keys/sbs_withbase64.cert" # provide certificate file in base64 format
           - key: RUNQ_ROOTDISK
             value: newroot
          ports:
           - containerport: 443
             protocol: tcp   
             hostport: 21443
          volumes:
           - name: qg_securebuild
             ref: np-medium
          mounts:
           - mountpoint: /data
             filesystem: ext4
             size: 16GB
             mount_id: data
           - mountpoint: /docker
             filesystem: ext4
             size: 16GB
             mount_id: docker
           - mountpoint: /newroot
             filesystem: ext4
             size: 10GB
             mount_id: newroot
             reset_root: false
        ```
        This Secure Build virtual server is used to build the MongoDB image.

        **Note**: You can view the example configuration files at `home/hpvs_user/HPVS12x_Production/config/yaml`.

2. Create the Secure Build virtual server by using the configurations in the yaml file.  
     ```
     hpvs deploy --config $HOME/hpvs/config/securebuild/securebuild.yaml --templatefile ../../templates/virtualserver.template.yml
     ```

    ??? example "Example of the command"

        ```
        hpvs deploy --config $HOME/hpvs/config/securebuild/securebuild.yaml --templatefile ../../templates/virtualserver.template.yml
        ```

   The deploy process automates creation of the quotagroup, network, and loads the Secure Dockerbuild image to the Secure Service Container LPAR. After the command completes execution, the Secure Build Server will be available at the IP Address of the Hyper Protect Virtual Server LPAR and port (GuestPort) specified.
   To showcase the sample application, this Secure Build virtual Server will be used to build the [MongoDB Docker image](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/build/#procedure-to-create-the-mongodb-image) later in this trial. You can follow the same procedure listed above, for creating a Secure Build virtual Server that will be used to build the [digital banking application Docker image](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/build/#procedure-to-create-the-digital-banking-image){target=_blank} later in this trial.


???+ example "Example Output"
    ![Create Server](securebuild-Images/create_server.png)



## Creating the virtual server for MongoDB

1. Create the configuration file `mongo_demo.yml` for the MongoDB virtual server by referring to the example `vs_configfile_readme.yaml` available at `$HOME/home/hpvs/config`.
 The following is an example of the configuration file:

    ??? example "mongo_demo.yml"

         ```
         version: v1
         type: virtualserver
         virtualservers:
         - name: test_mongo
           host: test2
           repoid: mongodemo
           imagetag: latest
           imagefile: /home/hpvs_user/hpvs/config/securebuild/regfiles/encryptedRepoRegistration_mongo.enc
           resourcedefinition:
              ref: small
           networks:
           - ref: external_network
             ipaddress: 129.40.15.19
           volumes:
            - name: qg_securebuild
              ref: np-medium
              mounts:
              - mount_id: data
                mountpoint: /data
                filesystem: ext4
                size: 10GB
         ```

    !!! tip
        Step 3 of "Procedure to create the MongoDB image" from the topic [`Securely Build your application`](build.md){target=_blank} generates the encrypted registration file `encryptedRepoRegistration_mongo.enc` that is used in the above example yaml file. You can view the example configuration files at `home/hpvs_user/HPVS12x_Production/config/yaml`.


2. Create the MongoDB virtual server by using the configurations in the yaml file (in this step, the MongoDB image is pulled from DockerHub).

     ```
     hpvs deploy --config $HOME/hpvs/config/vs_configfile_readme.yml --templatefile ../../templates/virtualserver.template.yml
     ```

    ??? example "Example of the command"

        ```
        hpvs deploy --config $HOME/hpvs/config/mongo_demo.yml --templatefile ../../templates/virtualserver.template.yml
        ```


    ???+ example "Example Output"
        ![Mongo Server](securebuild-Images/mongo_vs.png)


## Creating a Secure Build virtual server to build the digital banking application

You can complete the [same steps](https://ibm-hyper-protect.github.io/hyper-protect-virtual-servers-hosted-trial/securebuild/create-server-hpvsdeploy/#creating-a-secure-build-virtual-server){target=_blank} for creating the Secure Build virtual server to build the digital banking application Docker image.

After you complete the steps to create the virtual server, this server is used to build the digital banking application Docker image, later in this trial.


!!! tip
    If you do not want to create another configuration file, you can delete the Secure Build virtual server that was earlier created (test_securebuild) for building the MongoDB image, and follow the same steps for creating a Secure Build virtual server .You can view the example configuration files at `home/hpvs_user/HPVS12x_Production/config/yaml`.



## Creating the virtual server for the digital banking application

The procedure is the same as the one you would use to create the virtual server for MongoDB, but use a configuration file that specifies the details that are required for the digital virtual server.

1. Create the configuration file for the digital banking application virtual server by referring to the example `vs_configfile_readme.yaml` available at `$HOME/home/hpvs/config`. This following is an example of the configuration file:

    ??? example "digital_demo.yml"

         ```
         version: v1
         type: virtualserver
         virtualservers:
         - name: test_digital
           host: test2
           repoid: digitaldemo
           imagetag: latest
           imagefile: /home/hpvs_user/hpvs/config/securebuild/regfiles/encryptedRepoRegistration_digital.enc
           resourcedefinition:
              ref: small
           networks:
           - ref: external_network
             ipaddress: 129.40.15.20
           environment:
           - key: LOGTARGET
             value: /dev/console
           - key: ROOTFS_LOCK
             value: "y"
           - key: ROOT_SSH_KEY
             value: '@/home/hpvs_user/.ssh/id_rsa'
           - key: RUNQ_ROOTDISK
             value: new_qg_digitalbank         
           volumes:
            - mounts:
              - filesystem: ext4
                mount_id: new_qg_digitalbank
                mountpoint: /newroot
                size: 10GB
              - filesystem: ext4
                mount_id: data
                mountpoint: /data
                size: 10GB
              name: qg_hpvs_digitalbank
              ref: np-medium
         ```


         **Note**: You can view the example configuration files at `home/hpvs_user/HPVS12x_Production/config/yaml`.

2. Create the digital application virtual server by using the configurations in the yaml file (in this step, the digital banking application image is pulled from DockerHub).

     ```
     hpvs deploy --config $HOME/hpvs/config/vs_configfile_readme.yml  --templatefile ../../templates/virtualserver.template.yml
     ```

    ??? example "Example of the command"

        ```
        hpvs deploy --config $HOME/hpvs/config/digital_demo.yml  --templatefile ../../templates/virtualserver.template.yml
        ```


    ???+ example "Example Output"
        ![Digital Server](securebuild-Images/digital_vs.png)


!!! note

       - You can assign IP addresses and hostnames for containers as necessary for your purposes but using the docker network and host ports is a nice way to quickly get running without having to use up IP addresses on your network.
       - You can use the `hpvs undeploy` command to delete a virtual server. For more information, see [`Undeploying virtual servers`](../reference/hpvs_undeploy.md){target=_blank}.  
       - You can update the resources or configuration of a virtual server after the completion of the deploy operation by using the `-u`, or the `--update` flag of the `hpvs deploy` command. For more information, see [`Updating virtual servers`](../reference/hpvs_update.md){target=_blank}.
