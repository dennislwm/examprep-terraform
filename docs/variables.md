# Input Variables
## Variable Definition Precedence

Terraform loads variables in the following order, with later sources taking precedence over earlier ones, i.e. higher number has greater precedence:

| Precedence |                     Variable Definition                      |
|:----------:|:------------------------------------------------------------:|
|     1      |              `TF_VAR_*` (environment variables)              |
|     2      |      `*.tfvars` or `*.tfvars.json` (alphabetical order)      |
|     3      | `*.auto.tfvars` or `*.auto.tfvars.json` (alphabetical order) |
|     4      |          `-var` or `-var-file` (command-line flags)          |
