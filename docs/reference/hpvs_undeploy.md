# Undeploying virtual servers

You can use the `hpvs undeploy` command to delete existing virtual server instances along with resources like networks, and quotagroups, that were allocated to that virtual server. Only resources that are not shared with other virtual servers are deleted. This command also deletes all images, and repositories that are not shared with other virtual servers.

This procedure is intended for users with the role _cloud administrator_.

## Procedure

On your x86 or Linux on IBM Z/LinuxONE (i.e., s390x architecture) management server, complete the following steps with root user authority.

- You can use the configuration yaml file `$HOME/hpvs/config/yaml/vs_hpvsopbasessh.yml` that you used for creating the virtual server instance. The following is an


??? example "Example of the `vs_hpvsopbasessh.yml` file"

    ```
    version: v1
    type: virtualserver
    virtualservers:
    - name: test-hpvsopbasessh
      host: SSC_LPAR_NAME
      hostname: hpvsopbasessh-container
      repoid: HpvsopBaseSSH
      imagetag: 1.2.1-abcdefg
      imagefile: HpvsopBaseSSH.tar.gz
      resourcedefinition:
         ref: small
      environment:
       - key: LOGTARGET
         value: "/dev/console"
       - key: ROOTFS_LOCK
         value: "y"
       - key: SSH_PUBLIC_KEY
         value: "@/root/hpvs/config/hpvsopbasessh/keys/id_rsa.pub"
      networks:
       - ref:  external_network
         ipaddress: 10.20.4.12
      volumes:
       - name: qg_hpvsopbasessh
         ref : np-small
         mounts:
          - mount_id: new_qg_hpvsopbasessh
            mountpoint: /newroot
            filesystem: ext4
            size: 10GB
       - name: qg_passthrough
         ref: p-small
         mounts:
          - mountpoint: /qg_passthrough
    ```

    **Note**: The configuration yaml file should specify either the `imagefile` parameter, or the `reporegfile` parameter, but not both.

- You can use the `hpvs vs list` command to view the list of virtual servers. You can undeploy only those virtual servers (and the  resources associated with the virtual server) that were created by using the `hpvs deploy` command, from this list.

- Run the following command to undeploy the virtual server.  
   ```
   hpvs undeploy --config $HOME/hpvs/config/yaml/vs_hpvsopbasessh.yml
   ```
   A message is displayed stating that virtual server(s) and associated networks, storage, images, and repository will be deleted. You are prompted to enter either `Yes` or `No`. If you enter `Yes`, the command execution continues, otherwise it exits the command execution.   
   When you have a large number of virtual servers to undeploy, you can use the following flags to simplify the undeploy operation:

   * `--exclude`: To exclude virtual servers from the undeploy operation. You can specify a single virtual server, or a comma separated list of virtual servers.
   * `--include`: To include the virtual servers from the undeploy operation. You can specify a single virtual server, or a comma separated list of virtual servers.
   * If you do not use the `--exclude` or `--include` flag, all virtual servers that are listed in the configuration yaml file are undeployed. The `--exclude` or `--include` flags are mutually exclusive and you must specify only one of them when you run the `hpvs undeploy` command.

- You can run the following commands to verify if the resources associated with the virtual server or virtual servers are deleted.
   * `hpvs image list` command to verify if the images associated with the virtual server(s) are deleted.
   * `hpvs network list` command to verify if the networks associated with the virtual server(s) are deleted.
   * `hpvs quotagroup list` command to verify if the quotagroups associated with the virtual server(s) are deleted.
   * `hpvs repository list` command to verify if the repositories associated with the virtual server(s) are deleted.

   **Note**: The resources that are shared by other virtual servers are not deleted and an appropriate message is displayed.
