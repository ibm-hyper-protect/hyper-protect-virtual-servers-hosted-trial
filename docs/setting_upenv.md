# Setting up the environment

If you want to use your own applications for exploring Secure Build, you must register the base images in the remote registry server (Docker Hub or IBM Cloud Container Registry) by using your Docker Hub or IBM Cloud Container Registry user ID and password.

The base images are the default Hyper Protect Virtual Server container images that can be used to host your application code, and include two different types of container images for your development and production environments.
  * `HpvsopBaseSSH`, which packages the SSH daemon into the default Hyper Protect Virtual Server container image, so that you can log in to the Hyper Protect Virtual Server by using the secure shell and your private key for debugging and development.
  * `HpvsopBase`, which excludes the SSH daemon on the default Hyper Protect Virtual Server container image, and can be used in the production environment.
In the hosted trial environment, the base images are located at `$HOME/hpvs/config/hpvsopbase/images`(`HpvsopBase`), and `$HOME/hpvs/config/hpvsopbasessh`(`HpvsopBaseSSH`).

## Before you begin

* Check that you have the account ID and password on the remote docker registry server to create repositories for base images. For example, `docker_base_user` is your user ID on the remote docker registry server.
*  Check that you have installed the GPG command line tool on the x86 or Linux on IBM Z/LinuxONE (i.e., s390x architecture) management server. For more information, see [GNU Privacy Guard](https://www.gnupg.org/index.html).
* Check that you have enabled Docker Content Trust (DCT) for your remote docker registry server. For more information, see [Content trust in Docker](https://docs.docker.com/engine/security/trust/content_trust/) or [Setting up your trusted content environment for IBM Container Registry](https://cloud.ibm.com/docs/services/Registry?topic=registry-registry_trustedcontent#registry_trustedcontent_dct_notary).
  ```
  export DOCKER_CONTENT_TRUST=1
  ```

## Procedure
Complete the following steps.

1. Create a GitHub account (if you do not already have one) [here](https://github.com/join?ref_cta=Sign+up&ref_loc=header+logged+out&ref_page=%2F&source=header-home){target=_blank}.

2. Create a Docker Hub account (if you do not already have one) by following the instructions [here](https://docs.docker.com/docker-id/){target=_blank}.

3. Create a Docker access token for Docker Hub access during the trial (you will delete it if after finishing the trial) by following the instructions [here](https://docs.docker.com/docker-hub/access-tokens/#create-an-access-token){target=_blank}.

4. Log in to the remote docker repository.  
    * For Docker Hub, run the `docker login` command from the `$HOME/hpvs_user` directory. For more information, see [Docker Login command](https://docs.docker.com/engine/reference/commandline/login/).
    * For IBM Cloud Container Registry, run `docker login -u iamapikey -p <iam_api_key> <region>.icr.io` command. For more information, see [Using Docker to authenticate with an API key](https://cloud.ibm.com/docs/Registry?topic=registry-registry_access#registry_access_apikey_auth_docker).

5. From the `$HOME/hpvs_user` directory, run the command: `docker images`, to check whether the base images are loaded into the local registry successfully.
   ```
   REPOSITORY                       TAG                       IMAGE ID            CREATED             SIZE
   ibmzcontainers/hpvsop-base-ssh   1.2.3-release-d0651e4     cd4e5704ae92        13 days ago         1.31GB
   ```

6. Create two repositories in your namespace for both the `hpvsop-base` image and the `hpvsop-base-ssh` image on the [Docker Hub](https://hub.docker.com/). For example, `docker_base_user/hpvsop-base` and `docker_base_user/hpvsop-base-ssh`. Note that the repository name must match the image name.

7. Use the `docker tag` command to tag base images with the same ID used by the CLI tool. Run the following commands from the `$HOME/hpvs_user` directory, to tag both base images.
   ```
   docker tag ibmzcontainers/hpvsop-base:1.2.3-release-d0651e4 docker_base_user/hpvsop-base:1.2.3-release-d0651e4
   docker tag ibmzcontainers/hpvsop-base-ssh:1.2.3-release-d0651e4 docker_base_user/hpvsop-base-ssh:1.2.3-release-d0651e4
   ```

8. Run the `docker images` command from the `$HOME/hpvs_user` directory, to check whether the tags for the base images are as expected.
   ```
   REPOSITORY                         TAG                       IMAGE ID            CREATED            SIZE
   ...
   ibmzcontainers/hpvsop-base         1.2.3-release-d0651e4     a53aae01b3ef        6 days ago         1.37GB
   docker_base_user/hpvsop-base       1.2.3-release-d0651e4     a53aae01b3ef        6 days ago         1.37GB
   ibmzcontainers/hpvsop-base-ssh     1.2.3-release-d0651e4     cd4e5704ae92        6 days ago         1.31GB
   docker_base_user/hpvsop-base-ssh   1.2.3-release-d0651e4     cd4e5704ae92        6 days ago         1.31GB
   ...
   ```
9. Push the base images to your remote docker repositories. For example:
   ```
   docker login
   docker push docker_base_user/hpvsop-base:1.2.3-release-d0651e4
   docker push docker_base_user/hpvsop-base-ssh:1.2.3-release-d0651e4
   ```

10. Document the credentials you will use to build your application with the Secure Build container.
    * Your Docker Hub ID account used to register the base images. For example, `docker_base_user`
    * Your Docker Hub ID password. For example, `passw0rd`
