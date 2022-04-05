# Explore the Hyper Protect Virtual Servers CLI


!!! info
    In this trial, you will use the Hyper Protect Virtual Servers CLI (`hpvs` command) to interact with the Hyper Protect Virtual Servers Hosting Appliance in order to perform the various actions necessary for Secure Build, or Bring Your Own Image. Here is a quick introduction to the commands available through this CLI.

1. See the different commands you could enter with:

    ``` bash
    hpvs --help
    ```

    ???+ example "Example Output"

        ``` bash
        IBM® Hyper Protect Virtual Servers, the evolution of the
        IBM® Secure Service Container for IBM® Cloud Private offering,
        protects Linux workloads on IBM Z and LinuxONE throughout their
        lifecycle build management and deployment.
        This solution delivers the security needed to protect
        mission critical applications in hybrid multi-cloud deployments.

        Usage:
        hpvs [command]

        Available Commands:
        crypto      Crypto command
        deploy      Deploy command
        help        Help about any command
        host        Host command
        image       Image Command
        network     Network command
        quotagroup  Quotagroup command
        regfile     Generate encrypted repository registration file. If you have already image build on s390x arch
        registry    Registry command
        repository  Repository command
        sb          SecureBuild command
        snapshot    Snapshot command
        version     Print hpvs version
        vs          Virtual Server command

        Flags:
            --debug                   If --debug is passed, it will enable debug logs
        -h, --help                    Help for hpvs
            --host string             Host LPAR name
            --log-output-dir string   Set log output directory

        Use "hpvs [command] --help" for more information about a command.
        ```

2. For further exploration of the Hyper Protect Virtual Servers CLI see the [IBM Documentation](https://www.ibm.com/docs/en/hpvs/1.2.x?topic=reference-commands-in-hyper-protect-virtual-servers){target_blank}.
