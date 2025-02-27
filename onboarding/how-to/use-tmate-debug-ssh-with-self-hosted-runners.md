(tmate-debug-ssh-self-hosted-runners)=
# Use tmate (debug-ssh) with self-hosted runners
**Source: [Discourse](https://discourse.canonical.com/t/use-tmate-debug-ssh-with-self-hosted-runners/3289)**

This guide covers how to setup [tmate action workflow](https://github.com/canonical/action-tmate) with Canonical's organization wide self-hosted runners.

## 1. Connect to Canonical VPN
- please read [IS How to: Company Open VPN](https://wiki.canonical.com/InformationInfrastructure/IS/HowTo/CompanyOpenVPN) and connect to Canonical's VPN.

##  2. Setup GitHub [SSH keys](https://github.com/settings/keys)
- Only actors with matching SSH keys will be authenticated to connect to the SSH session.

```{image} images/use-tmate-debug-ssh-with-self-hosted-runners-1.png
:alt: ssh-gpg-key-access-menu
:align: center
```

- Profile > settings > SSH and GPG keys
## 3. Setup repository workflow with [canonical/action-tmate](https://github.com/canonical/action-tmate). 
* *note that this is a fork of [`mxschmitt/action-tmate`](https://github.com/mxschmitt/action-tmate) which has been modified to work with Canonical VPN with automatic configuration.
* example [workflow in use](https://github.com/canonical/operator-workflows/blob/main/.github/workflows/integration_test_run.yaml#L243-L246)
* sample workflow on failure
    ```
    name: Canonical action-tmate test
        
    on: [pull_request]
        
    jobs:
      build:
        runs-on: [self-hosted, linux, X64, jammy, large]
        steps:
        - uses: actions/checkout@v3
        - name: Setup tmate session
          if: ${{ failure() }}
          uses: canonical/action-tmate@main
    ```
## 4. Connect & debug!
* The output of the action would look similar to the following:

```{image} images/use-tmate-debug-ssh-with-self-hosted-runners-2.png
:alt: output-action
:align: center
```

    ```
    Run canonical/action-tmate@main
    Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease
    Hit:2 http://archive.ubuntu.com/ubuntu jammy InRelease
    Hit:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
    Hit:4 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
    Reading package lists...
    Reading package lists...
    Building dependency tree...
    Reading state information...
    xz-utils is already the newest version (5.2.5-2ubuntu1).
    xz-utils set to manually installed.
    openssh-client is already the newest version (1:8.9p1-3ubuntu0.6).
    openssh-client set to manually installed.
    0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    ssh -p10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179
    
    SSH: ssh -p 10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179
    or: ssh -i <path-to-private-SSH-key> -p10022 Nuw8ED2ymQsP6TcZ7LzXFDr8F@10.163.212.179
    ```
* The ssh command would be in the format of `ssh -p 10022 <session-token>@<canonical-internal-tmate-ssh-server-ip>`. Port 10022 is used since port 22 is already being used by Juju.
* 
```{image} images/use-tmate-debug-ssh-with-self-hosted-runners-3.png
:alt: ssh-session-output
:align: center
```

*
```{image} images/use-tmate-debug-ssh-with-self-hosted-runners-4.png
:alt: hello-world-output
:align: center
```


## Further information

For any questions/inquiries, please contact us at [Github Actions self-hosted runners](https://chat.canonical.com/canonical/channels/github-actions-self-hosted-runners) channel on Mattermost.



