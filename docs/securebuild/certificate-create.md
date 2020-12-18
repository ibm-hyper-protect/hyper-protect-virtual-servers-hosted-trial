# Create Certificate and Key

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
