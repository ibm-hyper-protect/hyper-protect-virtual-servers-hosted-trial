# Generating the signing keys

You can generate the key pair for signing the repository registration file by using the GnuPG tool.

This procedure is intended for users with the role _cloud administrator_ and _app developer or ISV_.


## Procedure

1. List the GPG keys by running the following command.
   ```
   gpg --list-keys
   gpg --list-secret-keys
   ```

2. The following commands create a GPG key pair, export the public key `isv_user.pub` and the private key `isv_user.private`. The key pair is protected by using the passphrase `over-the-lazy-dog`. If `isv_user` is listed when you run the `gpg --list-keys` command, then you must use another name.
   ```
   export keyName=isv_user
   export passphrase=over-the-lazy-dog
   cat >isv_definition_keys <<EOF
        %echo Generating registration definition key
        Key-Type: RSA
        Key-Length: 4096
        Subkey-Type: RSA
        Subkey-Length: 4096
        Name-Real: isv_user
        Expire-Date: 0
        Passphrase: over-the-lazy-dog
        # Do a commit here, so that we can later print "done" :-)
        %commit
        %echo done
   EOF
   gpg -a --batch --generate-key isv_definition_keys
   gpg --armor --pinentry-mode=loopback --passphrase  ${passphrase} --export-secret-keys ${keyName} > ${keyName}.private
   gpg --armor --export ${keyName} > ${keyName}.pub
   ```
   The "export keyName=isv_user" and "Name-Real: isv_user" must be unique. You cannot use the same keys to sign multiple images. You should not have multiple keys with same username, also you should not have multiple images singed with same key in a Secure Service Container.

2. Copy the generated key pair `isv_user.pub` and `isv_user.private` to the `<$HOME/hpvs>/config` directory on your x86 or Linux on IBM Z/LinuxONE (i.e., s390x architecture) management server.
