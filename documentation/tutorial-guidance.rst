Guidance on writing tutorials
=============================

Single sentence about what a tutorial intends: end-to-end user experience, complete journey, teach someone

For a tutorial in the style "Deploy X charm for the first time", the tutorials should contain the following sections:

* What you'll do: 
* Requirements: Suggest Multipass VM for easy cleanup. 
* Set up tutorial model: Create Juju model so that you can reference it throughout the tutorial
* Deploy the charm + its dependencies. Integrate. Required configurations. Explain! Try to split up as many commands as possible.
* Check that the deployment happened: ``juju status`` and output. If check MicroK8s pod, use the Juju model name
* Some final checkpoint: Visit a website, change a configuration, or run an action (maybe some combination of the three). Check that it worked (obvious for website, not so much for configuration or action)
* Tear down the environment: Destroy the Juju model, delete the Multipass VM
