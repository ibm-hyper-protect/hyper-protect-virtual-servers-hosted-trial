# Updating virtual servers

You can update the resources or configuration of a virtual server after the completion of the deploy operation by using the `-u`, or the `--update` flag of the `hpvs deploy` command. The information about the parameters to be updated are read from the configuration yaml file. You can edit the configuration file with the details of the update you want to perform and use this configuration file to run the command.

This procedure is intended for users with the role _cloud administrator_.

## Procedure

On your x86 or Linux on IBM Z/LinuxONE (i.e., s390x architecture) management server, complete the following steps with root user authority.

```
hpvs deploy --update --config $HOME/hpvs/config/demo_byoi.yml
```

**Note**: It is recommended that you back up the Hyper Protect Virtual Server container by using the `hpvs snapshot` command before you run the `hpvs deploy update` command.

When you have a large number of virtual servers to update, you can use the following flags to simplify the deploy update operation:

* `--exclude`: To exclude virtual servers from the deploy update operation. You can specify a single virtual server, or a comma separated list of virtual servers.
* `--include`: To include the virtual servers from the deploy update operation. You can specify a single virtual server, or a comma separated list of virtual servers.
* If you do not use the `--exclude` or `--include` flag, all virtual servers that are listed in the configuration yaml file are updated.
  The `--exclude` or `--include` flags are mutually exclusive and you must specify only one of them when you run the `hpvs deploy` command along with the `--update` flag.

  You can use the `--update` flag of the `hpvs deploy` command in the following scenarios:   

  - Increase the size of the mountpoint (you might need to increase the size of the quotagroup to accommodate the increase in mountpoint size).
  - Update the repository definition file.
  - Update the network by modifying the network config section in configuration yaml file. If the network not exist, a new network can be created with the specified details. Similarly, you can change an existing IP address.      

  You cannot use the `--update` flag of the `hpvs deploy` command in the following scenarios:  

  - Add a new mount ID, reduce the size of the mountpoint or reduce the size of the quotagroup.  
  - Detach a quotagroup (you cannot detach a quotagtoup by using the `hpvs vs update` command as well). Doing so might cause errors or lead to an irrecoverable state of the quotagroup and the virtual server.

  **Note**:
   
  * Networks that are detached when you run the `hpvs deploy` command by specifying the `--update` flag, are deleted if they not used by any other virtual server.  
  * You cannot update the settings of an existing network in the virtual server template file.
