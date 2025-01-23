# How to configure a terraform plan on the IS-charms bastion
**Source: [Discourse](https://discourse.canonical.com/t/how-to-configure-a-terraform-plan-on-the-is-charms-bastion/2275/4)**

The models are managed by Terraform using the [Juju Terraform Provider](https://github.com/juju/terraform-provider-juju). After creating the terraform plan and merging it on [canonical-terraform-plans](https://git.launchpad.net/canonical-terraform-plans/) then you need to configure it on the bastion.

Here are the steps:

1. Clone the canonical-terraform-plans repo

`git clone git+ssh://git.launchpad.net/canonical-terraform-plans`

2. Create a link named "plan" to your terraform plan

`ln -s /home/stg-synapse/canonical-terraform-plans/chat-synapse/environments/stg/ plan`

3. Use plan as working directory. Now load the OpenStack credentials to create a bucket where the state will be stored. It must match what is defined in `backend.tfvars` file.
```
    cd plan
    load_creds openstack
    swift post stg-synapse-tfstate
```
4. Load the S3 credentials and init the plan.
```
    load_creds s3
    https_proxy=http://squid.internal:3128 NO_PROXY=radosgw.ps5.canonical.com terraform init -backend-config=backend.tfvars
```

Note that NO_PROXY needs to be changed to radosgw.ps5.canonical.com if the environment is PS5.

5. Import the model 

This is needed to not let terraform try to destroy an existing model (if you don't have permissions it will fail or...actually destroy it while applying the plan).

   There are two ways of doing this: 

   - Running  the command `terraform import`
   ```
   terraform import module.synapse.juju_model.synapse stg-synapse
   ```
   - Adding the import to your plan. This is an example that might be adapted to your project:
   ```
   variable "juju_model_name" {
     description = "Juju model name"
     type        = string
   }

   import {
     to = juju_model.synapse
     id = var.juju_model_name
   }

   resource "juju_model" "synapse" {
      name = var.juju_model_name
   }
   ```
   Then you can set the value in the ```environments/<env>/main.tf``` or even use an environment variable (```export TF_VAR_juju_model_name="my-model"```) before running the ```terraform apply``` command.

6. Optional: If you have existing resources you can import them like in the following commands. Check the Juju Terraform Provider [documentation](https://registry.terraform.io/providers/juju/juju/latest/docs) to see how the id is formed for each resource.
```
    terraform import module.synapse.juju_application.synapse synapse
    terraform import module.synapse.juju_application.synapse stg-synapse.synapse
    terraform import module.synapse.juju_application.synapse stg-synapse:synapse
    terraform import module.synapse.juju_application.nginx_ingress_integrator stg-synapse:nginx-ingress-integrator
    terraform import juju_application.postgresql_k8s stg-synapse:postgresql-k8s
```
7. Run a terraform plan to check what will happen.

`terraform plan`

8. After checking, then run the apply to :drum:  apply the changes.

`terraform apply`

9. Check the status by using the following command:

`juju status`

Don't forget to edit the file `~/.envrc` changing the `TERRAFORM_PLAN` var accordingly.
