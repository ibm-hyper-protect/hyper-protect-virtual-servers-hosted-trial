# Create your Secure Build Server

Complete the steps detailed in this topic to create the Secure Build virtual server.


## Creating the certificate and key to securely communicate with secure build server

1. Run the following command.
   ```
   cd $HOME/hpvs/config/securebuild/keys
   ```

2. Create the certificate and key to securely communicate with secure build server.
   ```
   openssl req -newkey rsa:2048 \
   -new -nodes -x509 \
   -days 3650 \
   -out sbs.cert \
   -keyout sbs.key \
   -subj "/C=GB/O=IBM/CN=johndoe.example.com"
    ```
   **Note**: If you see errors like `random number generator:RAND_load_file:Cannot open file`, then run the following commands.
   ```
   openssl rand -out $HOME/.rnd -hex 256
   ```
3. Run the following command to change the certificate to base64 encoding.
   ```
   echo $(cat sbs.cert | base64) | tr -d ' ' >> sbs_base64.cert
   ```

## Create Quotagroup with storage for secure build server

``` bash
hpvs quotagroup create --name "qg_securebuild" --size=40GB
```

???+ example "Example Output"

    ``` bash
    +-------------+----------------+
    | name        | qg_securebuild |
    | filesystem  | btrfs          |
    | passthrough | false          |
    | pool_id     | lv_data_pool   |
    | size        | 40GB           |
    | available   | 40GB           |
    | containers  | []             |
    +-------------+----------------+
    ```

## Create Secure Build server

``` bash
hpvs vs create --name test_securebuild --repo SecureDockerBuild \
--tag 1.2.2.1-release-4dbd783 --cpu 2 --ram 2048 \
--quotagroup "{quotagroup = qg_securebuild, mountid = new, mount = /newroot, filesystem = ext4, size = 16GB}" \
--quotagroup "{quotagroup = qg_securebuild, mountid = data, mount = /data, filesystem = ext4, size = 16GB}" \
--quotagroup "{quotagroup = qg_securebuild, mountid = docker, mount = /docker, filesystem = ext4, size = 16GB}" \
--env={EX_VOLUMES="/docker,/data",ROOTFS_LOCK=y,CLIENT_CRT=$cert} \
--ports "{containerport = 443, protocol = tcp, hostport = 21443}"
```

???+ example "Example Output"
  ![Create Server](securebuild-Images/create_server.png)

We can see that the quotagroup is now being used

``` bash
hpvs quotagroup show --name "sb_user"
```

???+ example "Example Output"

    ``` bash
    +-------------+--------------------------------+
    | PROPERTIES  | VALUES                         |
    +-------------+--------------------------------+
    | name        | qg_securebuild                 |
    | filesystem  | btrfs                          |
    | passthrough | false                          |
    | pool_id     | lv_data_pool                   |
    | size        | 40GB                           |
    | available   | 12GB                           |
    | containers  | Container:test_securebuild     |
    |             | Mountids:"new","data","docker" |
    |             |                                |
    |             |                                |
    +-------------+--------------------------------+
    ```
Your secure build server is now up and running!

It is available at the IP Address of the Hyper Protect Virtual Server LPAR and port (GuestPort) specified. The application used for this SBS trial requires 2 virtual servers to be created, one for the digital banking application image, and another one for the Mongo DB Docker image.
You will use these secure build servers to securely build your application in the next section.

You will use this secure build server to securely build your application in the next section.

!!! note
    You can assign IP addresses and hostnames for containers as necessary for your purposes but using the docker network and host ports is a nice way to quickly get running without having to use up IP addresses on your network.
