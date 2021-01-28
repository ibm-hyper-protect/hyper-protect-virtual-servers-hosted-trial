# Deploy your Securely Built Application as a Hyper Protect Virtual Server


1. Register the repository on the secure service partition for the application image by using the generated repository registration file. See [Securely Build your Application](build.md){target=_blank} for details of how this repository registration file is generated.
   ```
   hpvs repository register --pgp=$HOME/hpvs/config/MyDockerAppImageRegfile.enc --id=MyDockerRepo
   ```

2. Create the quotagroup of the application image on the {{site.data.keyword.ssc}} partition.
   ```
   hpvs quotagroup create --name myquotagroup --size=30GB
   ```

   **Note**: If  you create a non-passthrough quotagroup, ensure that you specify a value that is at least 5 GB greater than the size you require for the virtual server.

3. Deploy the application image into the {{site.data.keyword.hpvs_full_notm}} as a Hyper Protect Virtual Server instance.
   ```
   hpvs vs create --name testcontainer --repo MyDockerRepo --tag latest --cpu 2 --ram 2048 --env={env_var1=value1,env_var2=value2} --quotagroup "{quotagroup = myquotagroup, mountid = new, mount = /newroot, filesystem = btrfs, size = 25GB}" --network "{name = external_net,ip = 10.20.4.73}"
   ```
