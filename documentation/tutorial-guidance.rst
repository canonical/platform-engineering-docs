Guidance on writing tutorials
=============================

A `tutorial <https://diataxis.fr/tutorials/>`_ is a learning-based, end-to-end
experience that provides a complete journey for the user.

The majority of our tutorials take on the form "Deploy X charm for the first
time". This document will provide guidance on constructing or editing a
tutorial of this type, although some parts of this document is still
applicable to a different type of tutorial.

Pieces of the tutorial
~~~~~~~~~~~~~~~~~~~~~~

The tutorial should contain the following pieces:

* **A title**. This title should be more descriptive than "Getting started"
  or "Quick guide" which are both indicative of a how-to guide. In a single
  statement, what will the user accomplish in the tutorial?
* **An introduction**. In a couple of sentences, introduce the charm and
  what the tutorial aims to do. Avoid using a statement like "you will learn",
  as that makes an assumption about the user's prior knowledge. Focus
  instead on what the user will do or accomplish. 
* **What you'll do**. This should be the first section in the tutorial. It
  aims to quickly and succinctly establish everything the user will do in the
  tutorial. 
* **Requirements**. This section should contain a list of resources, software
  or other tools that the user will need before starting the tutorial. In
  this section, provide *at least* the list below. If the tutorial supports
  architecture beyond amd64, update accordingly. If the tutorial involves
  a machine charm, update the type of controller that the user will need. If
  there is some reason why a Multipass VM is not acceptable for the tutorial,
  provide an alternate guide to help the user with installing Juju and
  bootstrapping a controller. Finally, include additional information if the
  tutorial will require significant resources (CPU, RAM, or disk space).

  * A working station, e.g., a laptop, with amd64 architecture.
  * Juju 3 installed and bootstrapped to a MicroK8s controller. You can
    accomplish this process by using a Multipass VM as outlined in this guide:
    `Set up / Tear down your test environment <https://juju.is/docs/juju/set-up--tear-down-your-test-environment>`_

* **Set up tutorial model**. The tutorial should begin at this point. Have
  the user create Juju model with a predetermined name so that you can reference
  the model name throughout the tutorial.
* **Deploy the charm + its dependencies**. If the charm requires any other
  charms to successfully deploy, either include in a single section or split into
  multiple sections. Use this pat of the tutorial to enable any required configurations.
  Make sure to briefly explain what the configurations or additional charms are
  and why the steps are necessary. Split up important commands into separate
  command blocks, and provide explanations between each command.
* **Check the deployment was successful**. Instruct the user to run
  ``juju status`` and show example output. If appropriate, also instruct the
  user to run ``kubectl get pods -n <juju model name>`` and show the output.
  Offer a brief explanation so that the user knows that the deployment was
  successful.
* **Some final checkpoint**. Have the user visit a website, change a
  configuration, or run an action (maybe some combination of the three). Check
  that this step worked; this is more obvious for a website, but you may need
  to be creative if you have the user change a configuration or run an action.
* **Tear down the environment**. This should be the final section. Take a moment
  to congratulate the user for getting through the tutorial! Have the user
  destroy the Juju model that they set up, and if applicable, instruct them to
  delete the Multipass VM.

Other guidance
~~~~~~~~~~~~~~

* Use section titles that summarize the action the user will take during that step.
  Avoid using present participles (for instance, "Deploying the charm") and
  instead opt for the infinitive (for the above example, use "Deploy the
  charm").
* Test your tutorial (either manually or using a spread test)! Or send the tutorial
  to another team member so they can test it!

Some example tutorials
~~~~~~~~~~~~~~~~~~~~~~

* `Wordpress tutorial <https://charmhub.io/wordpress-k8s/docs/tutorial>`_
* `Mattermost tutorial <https://charmhub.io/mattermost-k8s/docs/tutorial-deploy-the-mattermost-charm-for-the-first-time>`_
* `Jenkins tutorial <https://charmhub.io/jenkins-k8s/docs/tutorial-getting-started>`_ 
