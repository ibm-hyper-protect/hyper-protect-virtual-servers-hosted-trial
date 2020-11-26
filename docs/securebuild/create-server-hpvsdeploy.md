# Create your HPVS Secure Build Server by using the YAML configuration file and `hpvs deploy` command

## Export Variables set in previous sections to current terminal session

Source `bashrc` to set necessary variables if unset

``` bash
source "${HOME}/.bashrc"
```

## Create Certificate and Key for server side certificate checking in tls

!!! info

    In this process you will create the `Secure Build Server Client Certificate and Key` referenced in the [key table](overview.md#fnref:2). You will keep the private key on your Linux vm and pass the corresponding certificate to the secure build server you are deploying in this section. Then, whenever you interact with the secure build server to build your applications and get your manifest files it will check that your private key matches the public key of the certificate (i.e. server side checking in mutual tls) so that only you (or others / CI tools with the necessary private key) can access the server to perform secure build operations (`hpvs sb` commands) on it (i.e. build, log, status, manifest, etc.).

1. Generate rand file

    ``` bash
    openssl rand -out "${HOME}/.rnd" -hex 256
    ```

2. Make `SB_DIR/sbs_keys` directory to store secure build server keys.

    ``` bash
    mkdir -p "${SB_DIR}/sbs_keys"
    ```

3. Generate cert and key to use mutual tls

    ``` bash
    openssl req -newkey rsa:2048 \
    -new -nodes -x509 \
    -days 3650 \
    -out "${SB_DIR}/sbs_keys/sbs.cert" \
    -keyout "${SB_DIR}/sbs_keys/sbs.key" \
    -subj "/C=US/O=IBM/CN=hpvs.example.com"
    ```

    ???+ example "Example Output"

        ``` bash
        Generating a RSA private key
        ................................+++++
        ...............................+++++
        writing new private key to '/home/multiarch-lab/securebuild-lab/sbs_keys/sbs.key'
        -----
        ```

4. Change cert to base64 encoding and save to new file.

    ``` bash
    echo $(cat "${SB_DIR}/sbs_keys/sbs.cert" | base64) \
    | tr -d ' ' > "${SB_DIR}/sbs_keys/sbs_base64.cert"
    ```

5. Save the cert as an environment variable

    ``` bash
    export cert=$(cat "${SB_DIR}/sbs_keys/sbs_base64.cert")
    ```

6. Check your cert with:

    ``` bash
    echo $cert
    ```

    ???+ success "It should be long and have no spaces"

        `LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURUVENDQWpXZ0F3SUJBZ0lVUERITlQ3Y3YrZWNzZ1Y0QnZHSmhhaVAyOERnd0RRWUpLb1pJaHZjTkFRRUwKQlFBd05qRUxNQWtHQTFVRUJoTUNWVk14RERBS0JnTlZCQW9NQTBsQ1RURVpNQmNHQTFVRUF3d1FhSEIyY3k1bAplR0Z0Y0d4bExtTnZiVEFlRncweU1EQTJNall3TXpVM016RmFGdzB6TURBMk1qUXdNelUzTXpGYU1EWXhDekFKCkJnTlZCQVlUQWxWVE1Rd3dDZ1lEVlFRS0RBTkpRazB4R1RBWEJnTlZCQU1NRUdod2RuTXVaWGhoYlhCc1pTNWoKYjIwd2dnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUURWbTBveGI5N3pnYTc2REtwWAptSC9XaWh0OGJCOUZMMEY0U3dxRjdhQ3I4RkZBUHhIYnFieVQvTVhZcVYwdGlWU1JNSHhDd2hhR1FlSXJhWDlKCjRVZ2FCaUEwQ2krWVFZWmF1SXcvVmI4SEtUYkxERC9SblMwai9SSFk1Tzl1TXJ3NXNpQzBydElTRXBwa1VFdFEKWGZTdGFDMU82bmlLUXY1NDZkRGdRRmVGbUI5WHZVeHdzK2pES1g4aW1XaWhIeERLYVN1cEkwRk9JQ094dVhVWQpZSTVPeWVrbDVNaGttMitVTjhFekowY3JtSGJTQnh2Rlg2amh4UkhWNTd6VjR2Tm5VTGQwL1ZCNTBxZ2ZtcmVPCk5PMUJOMGtOMk1vV1N1R3lRdDZNS0NvSHAyUzgxTmJMcXRERndjMkxtbGxkc21hVEFqTVdkTjFyNjlXdVlkTmUKYjlJaEFnTUJBQUdqVXpCUk1CMEdBMVVkRGdRV0JCVFMrNEJKTUhZbG9RS2kvZjVlR2N1MDg3U3Q4VEFmQmdOVgpIU01FR0RBV2dCVFMrNEJKTUhZbG9RS2kvZjVlR2N1MDg3U3Q4VEFQQmdOVkhSTUJBZjhFQlRBREFRSC9NQTBHCkNTcUdTSWIzRFFFQkN3VUFBNElCQVFDQk9KdjVkbllLSUpmdXQ3VlVyWS9FYThzby9oaDhTdTQwK0NoRTYreVcKazFPSkV6NnorU3FrTFlwZ0hqV1JQL1VVY3ZzdCtnZVVNR2dZeThCZ01HaGFRVUdESm9NYzdvcDcyN2w1akdMcApjWGE3cDU0aWtqYnN3QzllL000a0tKRVVRWTEyUU42TDQrVDZBRUtZcnRUVnZCMURGLzhndW9ZNjFLbDEzOVNFCmVockllWGE4THVGTmY3STc1bHpJZHpTSHpieXp0empUeW9IYTN1cmloNUNnMkhISkpsTXlNaVIwQUVKMURMQmMKMmo0SFp2V0hrYWZaTEtuRnBKUHJNa01DSXpwdFQ1SnlHOW5pUlh3RmkxdzR6UDlJRHBCRVh1UGNuU1ZacFJFawpRSHNrSzY1Nm1xRndvY2pBMXNSMmZjS1pUbkJjMlhmM0NUelVxSVoyYStSVQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==`

## Set your provided number and save it for later use

You will be assigned a number for the lab so as not to interfere with other users.

!!! note
    [This table](../assignment.md){target=_blank} tells you which number you are assigned.

1. Set the `HPVS_NUMBER` variable with your assigned 2 digit number

    === "Command Syntax"

        ``` bash
        export HPVS_NUMBER="your_assigned_number"
        ```

    === "Example Command"

        ``` bash
        export HPVS_NUMBER="00"
        ```

    !!! warning
        Your user will **NOT** be `00`. Please set the appropriate user you have been assigned.

2. Save your number to `bashrc` for later use.

    ``` bash
    echo "export HPVS_NUMBER='${HPVS_NUMBER}'" >> "${HOME}/.bashrc"
    ```

3. Set Secure Build Server Port

    ``` bash
    export SB_PORT=300${HPVS_NUMBER}
    ```

4. Save `SB_PORT` to `bashrc` for later use.

    ``` bash
    echo "export SB_PORT='${SB_PORT}'" >> "${HOME}/.bashrc"
    ```

5. You can create the Secure Build virtual server by using the `hpvs deploy` command by specifying a configuration yaml file as an input for the `hpvs deploy` command. You can update the template file `$HOME/hpvs/config/templates/virtualserver.template.yml` based on the networking configuration, quotagroup, and resource settings of the Hyper Protect Virtual Server instance if necessary.

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

   - The `vs_securebuild.yml` that has the configuration details for the virtual server refers to the corresponding sections of the `virtualserver.template.yml` when you run the `hpvs deploy` command. For example, the `resourcedefinition: ref` value refers to the `resourcedefinitiontemplate` definition in the template file. The `network: ref` value refers to the `networktemplates` definition in  the template file. The following is an example of the virtual srever template file.  
    Create the configuration yaml file $HOME/hpvs/config/securebuild/demo_securebuild.yml for the instance by referring to the example file $HOME/hpvs/config/securebuild/vs_securebuild.yml. The following is an example of a `vs_securebuild.yml` file. In this example, the network definition is for an external network.


??? example "Example of a Secure Build virtual server configuration file"

    ```
    version: v1
    type: virtualserver
    virtualservers:
    - name: securebuildserver
      host: SSC_LPAR_NAME
      repoid: SecureDockerBuild
      imagetag: 1.2.2.1-release-4dbd783
      imagefile: SecureDockerBuild.tar.gz
      resourcedefinition:
         ref: small
      environment:
       - key: ROOTFS_LOCK
         value: "y"
       - key: CLIENT_CRT
         value: "@/root/hpvs/config/securebuild/keys/sbs_base64.cert" # provide certificate file in base64 format
       - key: RUNQ_ROOTDISK
         value: newroot
      networks:
       - ref:  external_network
         ipaddress: 10.20.4.67
      volumes:
       - name: securebuild_qg
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


   - Create the Secure Build virtual server by using the configurations in the yaml file.  
     ```
     hpvs deploy --config $HOME/hpvs/config/securebuild/demo_securebuild.yml
     ```

Your secure build server is now up and running.

It is available at the IP Address of the Hyper Protect Virtual Server LPAR and port (GuestPort) specified.

You will use this secure build server to securely build your application in the next section.

!!! note
    You can assign IP addresses and hostnames for containers as necessary for your purposes but using the docker network and host ports is a nice way to quickly get running without having to use up IP addresses on your network.
