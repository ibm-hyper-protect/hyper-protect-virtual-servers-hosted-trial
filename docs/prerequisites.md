# Prerequisites

The Hosted trial environment already has sample applications that you can try for the Secure Build, and Bring Your Own Image features. However, if you would like to use your own applications, you must complete the tasks detailed in [Setting up the environment](setting_upenv.md){target=_blank}, before you proceed.

## Accessing the Linux management server and the Secure Service Container Partition
You will be provided the details to connect to the Linux management server by using the VPN. The details about the IP addresses, username, and password will be shared with you by email.

## Support contact
You can contact IBM support personnel by using the slack channel details that are shared with you by email, for troubleshooting or queries about using IBM Hyper Protect Virtual Servers.

## Creating the server configuration YAML file

You can find examples of the virtual server template file and virtual server configuration files in the *$HOME/hpvs/config/* directory. The  virtual server configuration files contain the configuration details of the virtual server, and this configuration file refers to the corresponding sections of the `virtualserver.template.yml` file when you run the `hpvs deploy` command.

The following is an example of the `virtualserver.template.yml` file that is present in the *$HOME/hpvs/config/templates/* directory.

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

You can find examples of the virtual server configuration file in the following topics:  
- [`Create your HPVS Secure Build Server by using a configuration file`](securebuild/create-server-hpvsdeploy.md){target=_blank}  
- [`Deploying your applications securely`](byoi/byoi.md){target=_blank}
