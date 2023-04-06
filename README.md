# Salt Project Native Minions for Solaris 10 salt-native-minion-solaris-11

## Overview

Salt Native Minion for Solaris 11 was originally developed on GitLab
Here are the instructions for GitLab, but in final releases, the Salt Native
Minion for Solaris 11 was built using a bash script on Intel and SPARC
machines.  Support is for Solaris 11.4 and higher

## Getting started

To make it easy for you to get started with GitLab, here's a list of recommended next steps.


## Add your files

- [ ] [Create](https://docs.gitlab.com/ee/user/project/repository/web_editor.html#create-a-file) or [upload](https://docs.gitlab.com/ee/user/project/repository/web_editor.html#upload-a-file) files
- [ ] [Add files using the command line](https://docs.gitlab.com/ee/gitlab-basics/add-file.html#add-a-file-using-the-command-line) or push an existing Git repository with the following command:

```
cd existing_repo
git remote add origin https://gitlab.com/saltstack/open/salt-native-minion-sunos-11.git
git branch -M main
git push -uf origin main
```

## Integrate with your tools

- [ ] [Set up project integrations](https://gitlab.com/saltstack/open/salt-native-minion-sunos-11/-/settings/integrations)

## Collaborate with your team

- [ ] [Invite team members and collaborators](https://docs.gitlab.com/ee/user/project/members/)
- [ ] [Create a new merge request](https://docs.gitlab.com/ee/user/project/merge_requests/creating_merge_requests.html)
- [ ] [Automatically close issues from merge requests](https://docs.gitlab.com/ee/user/project/issues/managing_issues.html#closing-issues-automatically)
- [ ] [Enable merge request approvals](https://docs.gitlab.com/ee/user/project/merge_requests/approvals/)
- [ ] [Automatically merge when pipeline succeeds](https://docs.gitlab.com/ee/user/project/merge_requests/merge_when_pipeline_succeeds.html)

## Test and Deploy

Use the built-in continuous integration in GitLab.

- [ ] [Get started with GitLab CI/CD](https://docs.gitlab.com/ee/ci/quick_start/index.html)
- [ ] [Analyze your code for known vulnerabilities with Static Application Security Testing(SAST)](https://docs.gitlab.com/ee/user/application_security/sast/)
- [ ] [Deploy to Kubernetes, Amazon EC2, or Amazon ECS using Auto Deploy](https://docs.gitlab.com/ee/topics/autodevops/requirements.html)
- [ ] [Use pull-based deployments for improved Kubernetes management](https://docs.gitlab.com/ee/user/clusters/agent/)
- [ ] [Set up protected environments](https://docs.gitlab.com/ee/ci/environments/protected_environments.html)

## Name

Salt support for Solaris 11.4 and above

## Description

This project allows you to build support for Salt on Solaris 11.4 and above, for both Intel and SPARC processors.

Provided is a .gitlab-ci.yml file to utilise GitLab CI/CD or alternatively can use the test_bld_xxxx Bash scripts

Provided in  CI/CD file and Bash scripts is a method to code sign the built packages, but you will need to provide your own certs and keys, examples of how to do that are provided using pkgsign. Also you will need to uncomment sections in the Bash script create_start_repository to 'require-signatures'.

The CI/CD file and Bash scripts generate a Solaris 11 p5p unsigned archive package which can be installed and removed with Solaris 11 IPS tools.

Documation on Salt 3005.1 for Solaris 11 can be found here:

    https://docs.saltproject.io/salt/install-guide/en/latest/topics/install-by-operating-system/solaris.html


## Installation

### Before installing the Solaris native minion:

Ensure that you have sufficient privileges to install packages on the Solaris system.
Check that your Solaris UNIX system is supported. See Solaris for more information.
Ensure that ports 4505 and 4506 are open on the applicable Solaris UNIX systems.
Salt uses ports 4505 and 4506 for outbound communication from the master to the minions. The Solaris native minion uses a direct connection to the Solaris UNIX system and uses the network interfaces for communication. For that reason, ports 4505 and 4506 need to be open on the appropriate network interfaces.

#### Install Salt on Solaris 11.4

The Solaris native minion package installs:

*   salt-minion service
*   salt-call service

Note: The salt-ssh and salt-proxy services are not installed with this package.

#### Salt minion package installation

The steps to install the Solaris native minion differ for Solaris 10 vs. Solaris 11. Ensure that you are using the correct set of instructions for your system.

If installing on a virtual machine, consult the documentation for your hypervisor as the commands might differ slightly.

#### To install the package on Solaris 11:

Ensure that you have sufficient privileges to install packages on the Solaris system.

In the terminal on the Solaris device, install Salt from the p5p archive. For example:

.. code-block:: bash

    pkg install -g file:///<path to p5p archive>/salt-|solaris-version|_solaris11_sparc.p5p  library/python/salt-minion

Use the following command to disable the salt-minion service, which is automatically started when installed:

.. code-block:: bash

    svcadm disable salt-minion

#### Configure and test the Solaris native minion

To configure the Solaris native minion to connect with its Salt master:

Edit the /opt/local/etc/salt/minion file to update the minion configuration with your environment’s specific details, such as the master’s IP address, the minion ID, etc. For example, to set the minion name:

id: your-solaris-minion-name

Edit the file to indicate the IP address of the master that is managing this minion. For example:

master: 192.0.2.1

Start the Solaris native minion with the following command:

.. code-block:: bash

    svcadm enable salt-minion

Once the Solaris native minion has been started and is running, you can use the command salt-key to verify the master has received a request for the minion key.

On the master, accept the minion’s key with the following command, replacing the placeholder test with the correct minion name:

.. code-block:: bash

    salt-key -y -a your-solaris-minion-name

After waiting a small period of time, verify the connectivity between the master and the Solaris native minion using simple commands. For example, try running the following commands:

.. code-block:: bash

    salt your-minion-name test.versions
    salt your-minion-name grains.items
    salt your-minion-name cmd.run ‘ls -alrt /’
    salt-call --local test.versions

You can now use the Solaris native minion. See Usage below for more information.

#### Salt minion package removal

To uninstall the Salt minion salt package on Solaris 11, run the following command:

.. code-block:: bash

    pkg uninstall library/python/salt-minion

## Usage

### Using the Solaris native minion

You can access the Salt command line interface on the Solaris native minion using executable Python scripts. These scripts execute with environmental variable overrides for library and Python paths. The wrapper scripts are located in the /usr/bin/salt-call and /usr/bin/salt-minion respectively.

Note: The Solaris native minion 3005.1 currently has scripts for:

.. code-block:: bash

    salt-minion
    salt-call

Salt command line functionality is available through the use of these scripts.

##### To start the minion:

.. code-block:: bash

    svcadm enable salt-minion

##### To stop the minion:

.. code-block:: bash

    svcadm disable salt-minion

You can also start the minion as a daemon using the following command:

.. code-block:: bash

    [/usr/bin/]salt-minion -d

## Support

Support can be found in various Salt communities, such as, Slack: https://saltstackcommunity.slack.com

## Contributing

Salt support on Solaris 11 is a community-run project and is open to all contributions.
The salt-native-minion-for-solaris-11 project team welcomes contributions from
the community. Before you start working with salt-native-minion-for-solaris-11,
please read our [Developer Certificate of Origin](https://cla.vmware.com/dco).
All contributions to this repository must be signed as described on that page.
Your signature certifies that you wrote the patch or have the right to pass it
on as an open-source patch. For more detailed information,
refer to [CONTRIBUTING.md](CONTRIBUTING.md).
The regular Open Source methods of contributing to a project apply:

*   Fork the project
*   Make your modifications to your fork
*   Provide tests for your modifications
*   Submit Merge/Pull Request to the project
*   Adjust modifications as per Reviewers of Merge/Pull Request

## Authors and acknowledgment

The initial work in porting Salt for the Solaris 11 platform was done by David Murphy damurphy@vmware.com

## License

Apache License 2.0

## Project status

The Salt native minion for Solaris 11.4 and above is now a community project.  In the past, VMware through Salt Project supported and developed Salt for Solaris 11, but VMware has now turned over on-going development to the community.

The project is currently seeking volunteers to step in as a maintainer or owner, to allow the project to keep going.
