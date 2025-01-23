# How to get started with the bastion systems
**Source: [Discourse](https://discourse.canonical.com/t/how-to-get-started-with-the-bastion-systems/3069)**

This is a quick guide on the basic instructions to get started with the bastion systems, used to host all of our models in both staging and production environments. 

This is targeted at onboarders who are looking for some brief instructions; note this is not official documentation and may be outdated. 

## Basic Outline
* Submitting IS Tickets
* Staging Access 
* SSH Into Bastion

## Submitting IS Tickets

To ssh into Prodstack, we need to submit a ticket to update our SSH key into Canonical's systems. Visit [IS requests](https://portal.admin.canonical.com/requests/) to submit a ticket, and then from the *Request Type* dropdown, separately file the following tickets:

* **'Request type | Update SSH Keys'**. Enter your public key and wait for approval. This will give us access to ssh into the prodstack systems.

* **'Request type | Get shell access on private-fileshare'** -- To get access to the bastions, you may also need to submit a ticket to get access to the private-filesystem, to share files internally with other people.

> *I don’t see the option from the dropdown!*
>
>If you don’t see one of the options, it is likely that you already have a pending request for that particular environment. Please complete the request first before proceeding with a new request. 

## Staging Access 

### Prerequisites 
- We have uploaded our public key to Launchpad (Go to your profile, and confirm your SSH key has been added correctly. If not, do so.

```{image} images/get-started-with-the-bastion-systems-1.png
:alt: bastion-systems
:align: center
```

### Adding username to `main.tf`

To get access to the staging environment, we need to submit an MP to the Launchpad repo. 

The MP should target the appropriate `main.tf` file, with respect to your team and required environment.  https://git.launchpad.net/canonical-terraform-plans/tree/prodstack/ps6/environments 

> Example: If you belonged to the `is-charms` team and wanted access to the `staging` environment, you would target https://git.launchpad.net/canonical-terraform-plans/tree/prodstack/ps6/environments/is-charms/staging/main.tf 

> Note that this example is for **ps6**, if you require access to **ps5** or some other server, ensure to target the correct destination.

Now, we can clone the repository with the following: `git clone git+ssh://<USERNAME>@git.launchpad.net/canonical-terraform-plans`

Replace `<USERNAME>` with your launchpad ID.

Open the file, navigate to the target file, and add the username to the team. For example, if you are joining me in Platform Engineering, you would add your username to the list as so:

`locals {
  is_charms_team = [ "user0", "user1", <USERNAME> ,"user2", "user3", ... ]
}
`

Ensure you commit your changes.

### Creating the merge proposal 

To fork this repository and propose fixes from there, push to this repository:

`git push git+ssh://codethulu@git.launchpad.net/~<USERNAME>/canonical-terraform-plans <BRANCHNAME>`

> https://code.launchpad.net/canonical-terraform-plans will generate the commands specific to your user, as well as provide recent information about branch modifications.

You should now be able to generate a merge proposal and add any necessary reviewers. 

## SSH Into Bastion

### Prerequisites 
* Ensure you have set up the [Company VPN](https://wiki.canonical.com/InformationInfrastructure/IS/HowTo/CompanyOpenVPN).

* The ```ssh-key``` we submitted to IS has been added to your local SSH agent, if we have not done so already: ```ssh-add ~/.ssh/<YOUR_SSH_KEY>```. 

  To confirm your SSH access works correctly, run the command:
```ssh -o PubkeyAcceptedKeyTypes=+ssh-rsa -i ~/.ssh/<YOUR_SSH_KEY> -l <USERNAME> -t mombin.canonical.com sl```.

  You should see a train move across your terminal.

> *It fails - I see ```Connection to mombin.canonical.com closed```*
>
> Ensure that you are connected to the Company VPN. You will be unable to SSH into internal systems without being connected to the proxy.

> *It fails - I see ```Permission denied (publickey)```*
>
> Check with IS that your account is ready. It is likely still in the process of being set up.

### Connection to bastion

We should be ready to ssh into the bastion system now. (You still need to be connected to the VPN!)

```ssh -v <USERNAME>@is-charms-bastion-ps6.internal```

> Once again, this example is for **ps6**. Change as required.

You should now have access to bastion!


### Footnotes
* A related post '[How to setup a new PS5/6 model](https://discourse.canonical.com/t/how-to-setup-a-new-ps5-6-model/3015)' inspired the general structure, and the level of detail of this post.

* https://wiki.canonical.com/InformationInfrastructure/IS/SSHebang is largely deprecated, but was referenced for SSH access to the system.

* Thank you to my team, who patiently helped me with this process during my onboarding! Hopefully this guide should help detail some of the basic steps for your own onboarding.

* If this post at any point becomes outdated, please let me know so I can amend it.


