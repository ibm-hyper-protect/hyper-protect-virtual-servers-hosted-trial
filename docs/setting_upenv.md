# Setting up the environment

When you want to use your own applications for the Secure Build, you must register the base images in the remote registry server (Docker Hub or IBM Cloud Container Registry) by using your Docker Hub or IBM Cloud Container Registry user ID and password. In the hosted trial environment, the base images are located at `/home/hpvs_user/HPVS1221_Production/images`.

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
    * For Docker Hub, run the `docker login` command. For more information, see [Docker Login command](https://docs.docker.com/engine/reference/commandline/login/).
    * For IBM Cloud Container Registry, run `docker login -u iamapikey -p <iam_api_key> <region>.icr.io` command. For more information, see [Using Docker to authenticate with an API key](https://cloud.ibm.com/docs/Registry?topic=registry-registry_access#registry_access_apikey_auth_docker).

5. Run the command: `docker images`, to check whether the base images are loaded into the local registry successfully. (**ToDo**: Add the output of this command)

6. Create two repositories in your namespace for both the `hpvsop-base` image and the `hpvsop-base-ssh` image on the [Docker Hub](https://hub.docker.com/). For example, `docker_base_user/hpvsop-base` and `docker_base_user/hpvsop-base-ssh`. Note that the repository name must match the image name.

7. Use the `docker tag` command to tag base images with the same ID used by the CLI tool.(**ToDo**: Check and add with dev on what the tags must be)

8. Run the `docker images` command to check whether the tags for the base images are as expected.(**ToDo**: Add the output of this command)

9. Push the base images to your remote docker repositories.(**ToDo**: Add the output of this command)

10. Document the credentials you will use to build your application with the Secure Build container.
    * Your Docker Hub ID account used to register the base images. For example, `docker_base_user`
    * Your Docker Hub ID password. For example, `passw0rd`