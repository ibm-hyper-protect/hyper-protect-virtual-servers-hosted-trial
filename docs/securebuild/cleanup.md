# Clean up your Environment

 You can complete the following steps to clean up your environment when you have finished exploring the features of this hosted trial.


## Cleanup Secure Build Server

1. Cleanup virtual server

    ``` bash
    hpvs vs delete --name <virtualserver_name>
    ```

2. Cleanup Quotagroup

    ``` bash
    hpvs quotagroup delete --name <quotagroup_name>
    ```

## Cleanup Application

1. Cleanup HPVS deployment

    ``` bash
    hpvs vs delete --name <repoid>
    ```
    Where <repoid> is the name of the repoid specified in the yaml file, for example: `repoid: SecureDockerBuild` in the `securebuild.yml` file.

2. Cleanup Quotagroup

    ``` bash
    hpvs quotagroup delete --name <repoid>
    ```

    Where <repoid> is the name of the repoid specified in the yaml file, for example: `repoid: mongodemo` in the `mongo_demo.yml` file


## Cleanup Repository

``` bash
hpvs repository  delete --id <repoid> --force
```

## Cleanup Docker Token

Delete the existing Docker Token by logging into your `Docker Hub` account and following [these instructions](https://docs.docker.com/docker-hub/access-tokens/#modify-existing-tokens){target=_blank}

## Cleanup GitHub SSH key

Delete the SSH key you added for the lab following steps 1-3 of [these instructions](https://help.github.com/en/github/authenticating-to-github/reviewing-your-ssh-keys){target=_blank}

<!--## Cleanup Lab directory

You can remove the directory you stored your files in throughout the lab. This is a personal choice as the lab machine will be deleted after the lab anyway.

``` bash
rm -rf "${SB_DIR}"
```
-->
