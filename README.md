Two repos (module and live)

See: https://github.com/Jay-Gokani/x_live_terraform

Module = contains TF version, AWS provider and version, resources and values in the form of locals, variables, outputs and hard-coded. The values used in the config are not environment specific.
A Git tag is assigned to each version of the modules, which is later called in Live. There may be only one branch in this repo, but different Git versions which are used by the Live repo.

Live = contains TF version, AWS provider and version, environment specific resources, module block calling the module in the other repo and values in the form of locals, variables, outputs and hard-coded. The values used in the config are not environment specific.
The values overwrite the module ones, so environment specific ones are only defined here.

The idea is to have resource types split into modules with general config in one repo and another repo split into environments then modules, which calls the config as modules and only provides values for environment specific values to overwrite the general ones defined.

Module structure:

main.tf
* Tf version
* Cloud provider
* Resources e.g. EC2, security groups, subnets (values are defined as variables, locals, data and minimally hardcoded)
* Tf state
* Locals (http_port, tcp_protocol etc)

variables.tf
* Most have no value defined (just a name, description and type), as the value will be given in the live main.tf
* Some with reasonable default, which can be overwritten in live

outputs.tf
* Name, value (referencing config in the resources), description
* To be used as inputs within the live main.tf


Live structure:

main.tf
* Tf version
* Cloud provider
* Module name and source (URL if a public source or SSH if private)
* Dependant on env:
    * Different config values like instance size, defined in variables.tf 
    * Additional resources like an autoscaling schedule, defined in variables.tf and 

variables.tf
* Provides inputs to main.tf
* Certain values were already defined in the module, so only env-specific ones are defined in live

outputs.tf
* Can be used to store values which are used elsewhere in live, but the one here isn't currently used
