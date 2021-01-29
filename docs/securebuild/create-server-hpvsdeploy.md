# Create your Secure Build Server using a yaml file

You can create the Secure Build virtual server by using the `hpvs deploy` command by specifying a configuration yaml file as an input for the `hpvs deploy` command.

1. See the topic [`Create your Secure Build Server`](create-server.md){target=_blank} for details about the certificate generation.

2. Update the template file `$HOME/hpvs/config/templates/virtualserver.template.yml` based on the networking configuration, quotagroup, and resource settings of the Hyper Protect Virtual Server instance if necessary.

    ??? example "Example of a virtualserver-template file"

    ```
    version: v1
    type: virtualserver-template
    networktemplates:
    -  name: external_network
       subnet: "10.20.4.0/22"
       gateway: "10.20.4.1"
       parent: "encf900"
       driver: "macvlan"
    -  name: internal_network
       subnet: "192.168.40.0/24"
       gateway: "192.168.40.1"
       parent: "encf900"
       driver: "bridge"
    quotagrouptemplates:
    # Passthrough quotagroup templates - A quotagroup will be dynamically created based
    # on the template and attached as single volume mount point to the virtual server.
    # Allowed filesystem types for the passthrough type quogagroup are btrfs, ext4, xfs
    -  name: p-small
       size: 20GB
       filesystem : ext4
       passthrough: true
    -  name: p-medium
       size: 50GB
       filesystem : ext4
       passthrough: true
    -  name: p-large
       size: 100GB
       filesystem : ext4
       passthrough: true
    -  name: p-xlarge
       size: 200GB
       filesystem : ext4
       passthrough: true
    -  name: p-xxlarge
       size: 400GB
       filesystem : ext4
       passthrough: true
    # Non passthrough quotagroup definitions - This quotagroups can be shared by
    # creating multiple volume mountpoints with the same virtual server or multiple
    # virtual server.  A non passthrough quotagroup will be dynamically created based
    # on the template and attached as volume mount points to the virtual server.
    # Only brtfs filesystem is supported in non passthrough quotagroups
    # mount points attached to virtual server can have filesystem btrfs, ext4, xfs
    -  name: np-small
       size: 20GB
       passthrough: false
    -  name: np-medium
       size: 50GB
       passthrough: false
    -  name: np-large
       size: 100GB
       passthrough: false
    -  name: np-xlarge
       size: 200GB
       passthrough: false
    -  name: np-xxlarge
       size: 400GB
       passthrough: false
    resourcedefinitiontemplates:
    -  name: default
       cpu: 1
       memory: 4096
    -  name: small
       cpu: 2
       memory: 6192
    -  name: large
       cpu: 4
       memory: 8192
    -  name: xl
       cpu: 8
       memory: 16656
    -  name: xxl
       cpu: 12
       memory: 32565   
    ```

3. Create the configuration yaml file $HOME/hpvs/config/securebuild/demo_securebuild.yml for the instance by referring to the example file $HOME/hpvs/config/securebuild/vs_securebuild.yml. The `vs_securebuild.yml` has the configuration details for the virtual server and refers to the corresponding sections of the `virtualserver.template.yml` when you run the `hpvs deploy` command. For example, the `resourcedefinition: ref` value refers to the `resourcedefinitiontemplate` definition in the template file. The `network: ref` value refers to the `networktemplates` definition in  the template file.   


    ??? example "Example of a Secure Build virtual server configuration file"

    ```
    version: v1
    type: virtualserver
    virtualservers:
    - name: test_securebuild
      host: test2
      repoid: SecureDockerBuild
      imagetag: 1.2.2.1-release-4dbd783
      imagefile: /home/hpvs_user/HPVS1221_Production/images/SecureDockerBuild.tar.gz
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


4. Create the Secure Build virtual server by using the configurations in the yaml file.  
     ```
     hpvs deploy --config $HOME/hpvs/config/securebuild/demo_securebuild.yml
     ```

??? example "Example of the command"

      ```
      hpvs deploy --config securebuild.yml
      ```

Your secure build server is now up and running. It is available at the IP Address of the Hyper Protect Virtual Server LPAR and port (GuestPort) specified. The application used for this SBS trial requires 2 virtual servers to be created, one for the digital banking application image, and another one for the Mongo DB Docker image. You will use these secure build servers to securely build your application in the next section.

???+ example "Example Output"
  ![Create Server](securebuild-Images/create_server.png)


!!! note

    - You can assign IP addresses and hostnames for containers as necessary for your purposes but using the docker network and host ports is a nice way to quickly get running without having to use up IP addresses on your network.
    - You can use the `hpvs undeploy` command to delete this virtual server. For more information, see [`Undeploying virtual servers`](../reference/hpvs_undeploy.md){target=_blank}.  
    - You can update the resources or configuration of a virtual server after the completion of the deploy operation by using the `-u`, or the `--update` flag of the `hpvs deploy` command. For more information, see [`Updating virtual servers`](../reference/hpvs_update.md){target=_blank}.
