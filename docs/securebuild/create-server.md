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
hpvs quotagroup create --name "sb_user" --size=40GB
```

???+ example "Example Output"

    ``` bash
    +-------------+--------------+
    | name        | sb_user      |
    | filesystem  | btrfs        |
    | passthrough | false        |
    | pool_id     | lv_data_pool |
    | size        | 40GB         |
    | available   | 40GB         |
    | containers  | []           |
    +-------------+--------------+
    ```

## Create Secure Build server

``` bash
hpvs vs create --name sbserver_ --repo SecureDockerBuild \
--tag 1.2.21-release-4dbd783 --cpu 2 --ram 2048 \
--quotagroup "{quotagroup = sb_user, mountid = new, mount = /newroot, filesystem = ext4, size = 10GB}" \
--quotagroup "{quotagroup = sb_user, mountid = data, mount = /data, filesystem = ext4, size = 2GB}" \
--quotagroup "{quotagroup = sb_user, mountid = docker, mount = /docker, filesystem = ext4, size = 16GB}" \
--env={EX_VOLUMES="/docker,/data",ROOTFS_LOCK=y,CLIENT_CRT=$cert} \
--ports "{containerport = 443, protocol = tcp, hostport = 30000}"
```

???+ example "Example Output"

    ``` bash
    ╭─────────────┬──────────────────────────────╮
    │ PROPERTIES  │ VALUES                       │
    ├─────────────┼──────────────────────────────┤
    │ Name        │ sbserver_00                  │
    │ Status      │ Up Less than a second        │
    │ CPU         │ 2                            │
    │ Memory      │ 2048                         │
    │ Networks    │ Network:bridge               │
    │             │ IPAddress:172.31.0.5         │
    │             │ Gateway:172.31.0.1           │
    │             │ Subnet:16                    │
    │             │ MacAddress:02:42:ac:1f:00:05 │
    │             │                              │
    │             │                              │
    │ Ports       │ LocalPort:443/tcp            │
    │             │ GuestPort:30000              │
    │             │                              │
    │ Quotagroups │ appliance_data               │
    │             │ sb_user00                    │
    │             │                              │
    │ State       │ running                      │
    ╰─────────────┴──────────────────────────────╯
    ```

We can see that the quotagroup is now being used

``` bash
hpvs quotagroup show --name "sb_user"
```

???+ example "Example Output"

    ``` bash
    +-------------+--------------------------------+
    | PROPERTIES  | VALUES                         |
    +-------------+--------------------------------+
    | name        | sb_user00                      |
    | filesystem  | btrfs                          |
    | passthrough | false                          |
    | pool_id     | lv_data_pool                   |
    | size        | 40GB                           |
    | available   | 12GB                            |
    | containers  | Container:sbserver_00          |
    |             | Mountids:"new","data","docker" |
    |             |                                |
    |             |                                |
    +-------------+--------------------------------+
    ```

The show output for the Hyper Protect Virtual Server was shown when it was deployed but we can bring it back up with

``` bash
hpvs vs show --name "sbserver"
```

???+ example "Example Output"

    ``` bash
    ╭─────────────┬──────────────────────────────╮
    │ PROPERTIES  │ VALUES                       │
    ├─────────────┼──────────────────────────────┤
    │ Name        │ sbserver                  │
    │ Status      │ Up About a minute            │
    │ CPU         │ 2                            │
    │ Memory      │ 2048                         │
    │ Networks    │ Network:bridge               │
    │             │ IPAddress:172.31.0.5         │
    │             │ Gateway:172.31.0.1           │
    │             │ Subnet:16                    │
    │             │ MacAddress:02:42:ac:1f:00:05 │
    │             │                              │
    │             │                              │
    │ Ports       │ LocalPort:443/tcp            │
    │             │ GuestPort:30000              │
    │             │                              │
    │ Quotagroups │ appliance_data               │
    │             │ sb_user00                    │
    │             │                              │
    │ State       │ running                      │
    ╰─────────────┴──────────────────────────────╯
    ```

Your secure build server is now up and running!

It is available at the IP Address of the Hyper Protect Virtual Server LPAR and port (GuestPort) specified.

You will use this secure build server to securely build your application in the next section.

!!! note
    You can assign IP addresses and hostnames for containers as necessary for your purposes but using the docker network and host ports is a nice way to quickly get running without having to use up IP addresses on your network.
