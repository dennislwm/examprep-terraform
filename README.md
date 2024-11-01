# examprep-terraform

---
# 1. Introduction
## 1.1. Purpose

This document describes the exam preparation for Terraform that is provided for Manager and DevSecOps.

## 1.2. Audience

The audience for this document includes:

* Developer who will design, code and test a custom provider with the Terraform Plugin Framework.

* DevSecOps who will learn, understand and apply any Terraform knowledge from KodeKloud and other courses.

* Manager who will manage, renew, or cancel a SaaS subscription to any courses.

---
# 2. System Overview
## 2.1. Benefits and Values

---
# 3. User Personas
## 3.1 RACI Matrix

|            Category            |                          Activity                           | Developer | DevSecOps | Manager |
|:------------------------------:|:-----------------------------------------------------------:|:---------:|:---------:|:-------:|
| Installation and Configuration |             Set up your development environment             |    R,A    |           |         |
| Installation and Configuration |              Installing Terraform with `tfenv`              |           |    R,A    |         |
| Installation and Configuration |               Constraining a Provider version               |           |    R,A    |         |
| Installation and Configuration |     Configuring a Provider multiple times with aliases      |           |    R,A    |         |
| Installation and Configuration |            Configuring a Remote Backend with S3             |           |    R,A    |         |
| Installation and Configuration |             Mocking a Remote Backend with Minio             |           |    R,A    |         |
| Installation and Configuration |               Upgrading a Terraform Provider                |           |    R,A    |         |
| Installation and Configuration |          Configuring a Remote Backend with Remote           |           |    R,A    |         |
| Installation and Configuration |    Determining Whether Run is Local or Remote Workspace     |           |    R,A    |         |
|           Execution            |                  Creating LifeCycle rules                   |           |    R,A    |         |
|           Execution            |         Creating identical resources using `count`          |           |    R,A    |         |
|           Execution            |        Creating identical resources using `for_each`        |           |    R,A    |         |
|           Execution            |            Mocking AWS Provider using Localstack            |           |    R,A    |         |
|           Execution            |                Creating a Provisioner block                 |           |    R,A    |         |
|           Execution            |          Including a source as a Terraform module           |           |    R,A    |         |
|           Execution            | Interacting with built-in functions using Terraform Console |           |    R,A    |         |
|           Execution            |            Using inline conditions in Terraform             |           |    R,A    |         |
|           Execution            |   Defining Terraform input variables based on precedence    |           |    R,A    |         |
|           Execution            |      Using a validation block within a variable block       |           |    R,A    |         |
|           Execution            |          Defining a Terraform input variable type           |           |    R,A    |         |
|           Execution            |            Defining a Terraform output variable             |           |    R,A    |         |
|           Execution            | Creating an implicit or explicit dependency for a resource  |           |    R,A    |         |
|           Execution            | Referencing an existing resource using Terraform data block |           |    R,A    |         |
|           Execution            |            Using a locals block for DRY strategy            |           |    R,A    |         |
|           Execution            |           Using a dynamic block for DRY strategy            |           |    R,A    |         |
|           Execution            |                      Creating a module                      |           |    R,A    |         |
|    Maintenance and Updates     |  Managing multiple environments using Terraform Workspace   |           |    R,A    |         |
|    Maintenance and Updates     |               Managing a Terraform state file               |           |    R,A    |         |
|    Maintenance and Updates     |              Tainting a Resource to replace it              |           |    R,A    |         |
|    Maintenance and Updates     |                   Checking Terraform logs                   |           |    R,A    |         |
|    Maintenance and Updates     |          Importing a real resource into Terraform           |           |    R,A    |         |
|    Maintenance and Updates     |       Using Terraform modules from a public registry        |           |    R,A    |         |
|    Maintenance and Updates     |  Managing multiple environments using Terraform Workspace   |           |    R,A    |         |
|    Maintenance and Updates     |         Targeting a resource during Terraform apply         |           |    R,A    |         |
|    Maintenance and Updates     |                 Publishing a public module                  |           |    R,A    |         |

---
# 4. Prerequisites
## 4.1. KodeKloud Pro

* [KodeKloud](https://kodekloud.com)
  - Registration Date: `26-Nov-23`
  - Recurring Amount: **$250 USD**
  - Next Due Date: `26-Nov-24`
  - Billing Cycle: `Annually`

## 4.2 Developer Workstation

* [Go 1.21+](https://golang.org/doc/install) installed and configured.
* [Terraform v1.5+](#51-installing-terraform-with-tfenv) installed locally.
* [Docker](https://www.docker.com/products/docker-desktop) and [Docker Compose](https://docs.docker.com/compose/install) to run an instance of your custom provider locally.

---
# 5. Installation and Configuration
## 5.1. Set up your development environment

This runbook should be performed by the Developer.

<details><summary> Click here to set up your development environment.</summary>

1. Open a terminal and `git clone` the [repository](https://github.com/hashicorp/terraform-provider-scaffolding-framework) and rename it to `terraform-provider-NAME`.

```sh
git clone https://github.com/hashicorp/terraform-provider-scaffolding-framework terraform-provider-NAME
```

2. Change into the cloned repository and rename the `go.mod` module.

```sh
go mod edit -module terraform-provider-NAME
```

3. Run `go mod tidy` to install all the provider's dependencies.

4. Open the `main.go` file in the root directory and replace the `import` declaration with the following:

```diff
import (
    "context"
    "flag"
    "log"

    "github.com/hashicorp/terraform-plugin-framework/providerserver"
-   "github.com/hashicorp/terraform-provider-scaffolding-framework/internal/provider"
+   "terraform-provider-NAME/internal/provider"
)
```

5. Create a `docker_compose/conf.json` file with the following:

```json
{
  "db_connection": "host=db port=5432 user=postgres password=password dbname=products sslmode=disable",
  "bind_address": "0.0.0.0:9090",
  "metrics_address": "localhost:9102"
}
```

6. Create a `docker_compose/docker-compose.yml` file with the following:

```yml
version: '3.7'
services:
  api:
    image: "hashicorpdemoapp/product-api:v0.0.22"
    ports:
      - "19090:9090"
    volumes:
      - ./conf.json:/config/config.json
    environment:
      CONFIG_FILE: '/config/config.json'
    depends_on:
      - db
  db:
    image: "hashicorpdemoapp/product-api-db:v0.0.22"
    ports:
      - "15432:5432"
    environment:
      POSTGRES_DB: 'products'
      POSTGRES_USER: 'postgres'
      POSTGRES_PASSWORD: 'password'
```

7. If you are stuck at any point, refer to the [provider branch](https://github.com/hashicorp/terraform-provider-hashicups/tree/provider) in the Terraform Hashicups Provider repository to see the changes implemented in the tutorial.
</details>

## 5.2. Installing Terraform with `tfenv`

This runbook should be performed by the DevSecOps.

<details><summary> Click here to install Terraform with tfenv.</summary>

1. Open a terminal and run `brew install tfenv`.
2. Run `tfenv --version` to check if installation was successful.

```sh
tfenv 3.0.0
```

3. Install the latest version of Terraform with `tfenv`.

```sh
tfenv install
tfenv use
```

4. Run `terraform --version` to check if installation was successful.

```sh
Terraform v1.7.5
on darwin_arm64
```
</details>

## 5.3. Constraining a Provider version

This runbook should be performed by the DevSecOps.

1. You can use `version` to constraint the version of a provider to a specific version. For example:

```tf
terraform {
  required_providers {
    local = {
      source = "hashicorp/local"
      version = "1.4.0"
    }
  }
}
```

2. You can use a logical operator to change the behaviour of `version`.
  - `!=`: do not use version, e.g. `version = "!= 1.4.0"`.
  - "<": below version.
  - ">": above version.

3. You can use a combination of logical operators to constraint the `version`. For example, `version = "> 1.2.0, < 2.0.0, != 1.4.0"`.

4. You can use a `~` operator to constraint the `version` to the last digit of a semantic `version`.
  - `~> x.x`: greedy minor version, e.g. `version = "~> 1.2"` will use the latest version that is `1.x` and below `2.0`.
  - `~> x.x.x`: greedy revision version, e.g. `version = "~> 1.2.1"` will use the latest version that is `1.2.x` and below `1.3`.

## 5.4. Configuring a Provider multiple times with aliases

This runbook should be performed by the DevSecOps.

1. You can have multiple configurations of the same provider using aliases. For example:

```diff
provider "aws" {
  region          = "us-east-1"
}

provider "aws" {
  region          = "ca-central-1"
+ alias           = "central"
}
```

2. You can specify which provider to use in your resource block as follows:

```diff
resource "aws_key_pair" "beta" {
  key_name        = "beta"
+ provider        = aws.central
}
```

## 5.5. Configuring a Remote Backend with S3

This runbook should be performed by the DevSecOps.

1. You can use a resource `terraform.backend` to configure a remote backend for Terraform state file. For example:

```tf
terraform {
  backend "s3" {
    bucket         = "terraform-state-bucket-01"
    key            = "finance/terraform.tfstate"
    region         = "us-west-1"
    dynamodb_table = aws_dynamodb_table.dynamodb-terraform-state-lock.name
  }
}
```

2. You can configure state locking by using a DynamoDB table with a primary key `LockID`, as follows:

```tf
resource "aws_dynamodb_table" "dynamodb-terraform-state-lock" {
  name = "state-locking"
  hash_key = "LockID"
  read_capacity = 20
  write_capacity = 20

  attribute {
    name = "LockID"
    type = "S"
  }
}
```

3. Save the above content in a file `terraform.tf`.

## 5.6. Mocking a Remote Backend with Minio

This runbook should be performed by the DevSecOps.

1. Modify your resource `terraform.backend` and add the following code:

```diff
terraform {
  backend "s3" {
    bucket         = "terraform-state-bucket-01"
    key            = "finance/terraform.tfstate"
    region         = "us-west-1"
    dynamodb_table = aws_dynamodb_table.dynamodb-terraform-state-lock.name
+   endpoint       = "http://172.16.238.105:9000"

+   force_path_style            = true
+   skip_credentials_validation = true
+   skip_metadata_api_check     = true
+   skip_region_validation      = true
  }
}
```

## 5.7 Upgrading a Terraform Provider

This runbook should be performed by the DevSecOps.

1. Run `terraform init -upgrade` to re-check the Terraform Registry for newer acceptable provider versions.

2. If available, Terraform downloads and saves them in a subdirectory under `.terraform/providers/`.

> Warning: If any acceptable version of a given provider is installed elsewhere, other than the correct automatic downloads directory `.terraform/providers/`, `terraform init -upgrade` will not download a newer version of it.

## 5.8. Configuring a Remote Backend with Remote

This runbook should be performed by the DevSecOps. The `cloud` backend includes an improved user experience and more features than the `remote` backend.

1. You can use a resource `terraform.backend` to configure a remote backend for Terraform state file. For example:
  * **hostname**: (Optional) The remote backend hostname to connect to. Defaults to `app.terraform.io`.
  * **organization**: (Required) The name of the organization containing the targeted workspaces.
  * **workspaces**: (Required) A block specifying one or more workspaces to use.

```tf
terraform {
  backend "remote" {
    hostname      = "app.terraform.io"
    organization  = "company"
    workspaces {
      name    = "my-app-prod"
    }
  }
}
```

2. The remote backend can work with either a single remote Terraform workspace, or with multiple same prefixed workspaces, e.g. `networking-dev`, `networking-prd`. The `workspaces.name` or `workspaces.prefix` determines which mode is used.

```tf
terraform {
  backend "remote" {
    hostname      = "app.terraform.io"
    organization  = "company"
    workspaces {
      prefix  = "my-app-"
    }
  }
}
```

## 5.9. Determining Whether Run is Local or Remote Workspace

This runbook should be performed by the DevSecOps. If you need to determine whether a run is local or remote workspace, use the HCP Terraform environment variable `HCP_TERRAFORM_RUN_ID`.

1. Edit your `variables.tf` file.

```tf
variable "HCP_TERRAFORM_RUN_ID" {
  type    = string
  default = ""
}
```

2. Edit your `outputs.tf` file.

```tf
output "remote_execution_determine" {
  value = "Remote run environment? %{if var.HCP_TERRAFORM_RUN_ID != ""}Yes%{else}No this is local%{endif}!"
}
```

---
# 6. Execution
## 6.1. Creating LifeCycle Rules

This runbook should be performed by the DevSecOps.

Terraform uses a lifecycle rule that aligns with an immutable infrastructure when provisioning resources. In other words, when updating a resource, Terraform will destroy the existing resource before replacing it with a new resource.

However, there may be cases where you may want to override this default setting. The `lifecycle` block allows you to set the behaviour of Terraform when updating a resource.

1. You can create a new resource first before destroying the old resource with `create_before_destroy`.

```tf
resource "local_file" "pet" {
  filename = "first"

  lifecycle {
    create_before_destroy = true
  }
}
```

2. You can prevent a resource from being destroyed during `apply` or `destroy` with `prevent_destroy`.

```diff
resource "local_file" "pet" {
  filename = "first"

  lifecycle {
-   create_before_destroy = true
+   prevent_destroy = true
  }
}
```

3. You can tell terraform do not update a resource for changes made to specific (or all) attributes in the list with `ignore_changes`.

```tf
resource "local_file" "pet" {
  filename = "first"
  tags = {
    "Name" = "first"
  }

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}
```

## 6.2. Creating identical resources using `count`

This runbook should be performed by the DevSecOps.

1. You can use `count` to create as many instances of a resource as the elements in a list. For example:

```tf
variable "my_list" {
  default = ["first","second","third"]
}

resource "local_file" "pet" {
  count    = length(var.my_list)
  filename = var.my_list[count.index]
}
```

2. If an element of the list is removed, or if the order of list is changed, terraform will force replacement of all resources of which the index in the list has changed. For example:

```diff
variable "my_list" {
- default = ["first","second","third"]
+ default = ["second","third"]
}
```

In this case, the `first` resource will be replaced by the `second`, and the `second` resource will be replaced by the `third`, while the `third` resource will be destroyed, as the resources are created as a list. Type `terraform show` to see the state resources.

```json
pets = [
  {
    "filename" = "second"
  },
  {
    "filename" = "third"
  }
]
```

## 6.3. Creating identical resources using `for_each`

This runbook should be performed by the DevSecOps.

1. You can use `for_each` to create as many instances of a resource as the keys to a map or set. For example:

```tf
variable "my_list" {
  type    = set(string)
  default = ["first","second","third"]
}

resource "local_file" "pet" {
  filename = each.value
  for_each = var.filename
}
```

2. Alternatively, you can use `for_each` on a list, if you make use of a built-in function `toset()`.

```diff
variable "my_list" {
- type    = set(string)
  default = ["first","second","third"]
}

resource "local_file" "pet" {
  filename = each.value
- for_each = var.filename
+ for_each = toset(var.filename)
}
```

3. If an element of the list is removed, terraform will only destroy the resource of which the element in the list was removed, as the resources are created as a map, and not as a list, in the state file. Type `terraform show` to see the state resources.

```json
pets = {
  "first" = {
    "filename" = "first"
  },
  "second" = {
    "filename" = "second"
  }
}
```

## 6.4. Mocking AWS Provider using Localstack

This runbook should be performed by the DevSecOps.

1. Create a `provider.tf` file and add the following code:

```diff
provider "aws" {
  region                      = "us-east-1"
+ skip_credentials_validation = true
+ skip_requesting_account_id  = true

+ endpoints {
+   iam                       = "http://localhost:4566"
+ }
}
```

## 6.5. Creating a Provisioner block

This runbook should be performed by the DevSecOps.

Terraform recommends to use provisioner blocks sparingly due to a few reasons:
* An increased complexity of the Terraform configuration.
* No way for `terraform plan` to accurately model the provisioner.
* Requires a `connection` block for `remote-exec`, which increases the security risk, similar to how Ansible runs its playbooks.

As an alternative, Terraform recommends to use:
* A native provisioner for some providers, e.g. `aws_instance.user_data`, which runs during creation time without a `connection` block.
* A custom AMI that embeds the provisioner block within it.

Having said that, you can use a `provisioner` block within a resource to run your custom script at the first boot of the instance.

1. You can run a custom script at creation time (default) of a resource using a nested provisioner block.

```tf
resource "aws_instance" "cerberus" {
  ami             = var.ami
  instance_type   = var.instance_type
  provisioner "remote-exec" {
    inline = ["install-nginx.sh"]
  }
  connection {
    type        = "ssh"
    host        = self.public_ip
    user        = "ubuntu"
    private_key = file("/root/.ssh/web")
  }
  key_name                = aws_key_pair.web.id
  vpc_security_group_ids  = [ aws_security_group.ssh-access.id ]
}
```

2. You can run a custom script at destroy time of a resource using the attribute `when`.

```diff
resource "aws_instance" "cerberus" {
  ami             = var.ami
  instance_type   = var.instance_type
  provisioner "remote-exec" {
+   when    = destroy
+   inline  = ["destroy-nginx.sh"]
  }
}
```

  **Note**: A failed execution of the provisioner will cause the `terraform apply` to fail and mark the resource as tainted.

3. You can run a custom script to continue upon failure using the attribute `on_failure`.

```diff
resource "aws_instance" "cerberus" {
  ami             = var.ami
  instance_type   = var.instance_type
  provisioner "remote-exec" {
    when        = destroy
+   on_failure  = continue
    inline      = ["install-nginx.sh"]
  }
}
```

  In this case, your resource will be created successfully even if the `provisioner` block fails.

4. You can run a custom script at creation time of a resource using the `user_data` attribute, that is native to the `aws_instance` resource and does not require a connection block.

```diff
resource "aws_instance" "cerberus" {
  ami             = var.ami
  instance_type   = var.instance_type
+ user_data       = <<-EOF
+                   install-nginx.sh
+                   EOF
}
```

> **Note**: The `user_data` when applied to an existing `aws_instance` will modify the resource without running the script, because this attribute will run only on the first instance boot.

## 6.6. Including a source as a Terraform module

This runbook should be performed by the DevSecOps.

Terraform modules are analogous to libraries or packages in other programming languages.

1. Assume `modules/payroll-app` consists of a Terraform configuration and `variables.tf` file.

```hcl
variable "app_region" {
  type    = string
}
variable "bucket" {
  type    = string
  default = "flexit-payroll-alpha-22001c"
}
variable "ami" {
  type    = string
}
```

2. Create a `us_payroll/main.tf` file and add the following code:

```diff
+module "us_payroll" {
+  source     = "../modules/payroll-app"
+  app_region = "us-east-1"
+  ami        = "ami-24e140119877avm"
+}
```

3. Run `terraform init` to download the `modules/payroll-app` in `us_payroll`.

4. Resources created from `modules/payroll-app` can be referenced using `module.us_payroll`, for example:

```hcl
module.us_payroll.aws_dynamodb_table.payroll.db
```

## 6.7. Interacting with built-in functions using Terraform Console

This runbook should be performed by the DevSecOps.

1. Run `terraform console` to open an interactive console, which will load the state and variables of your Terraform configuration file.

2. Run `file("root/terraform-projects/main.tf)` to read the contents of a file.

```sh
resource "aws_instance" "development" {
  ami           = "ami-xxx"
  instance_type = "t2.micro"
}
```

3. Run `length(var.region)` to show the length of a variable.

```sh
3
```

4. Run `toset(var.region)` to transform a list to a set (no duplicates).

```sh
[
  "ca-central-1",
  "us-east-1"
]
```

5. Run `max(var.num...)` to return the max, where `...` expands the set `var.num`.

```sh
250
```

6. Run `split(",","ami-1,ami-2,ami-3")` to transform a delimited string to a list.

```sh
["ami-1","ami-2","ami-3"]
```

7. Run `index(var.ami, "ami-2")` to find the index of an item.

```sh
1
```

8. Run `contains(var.ami, "ami-4")` to check whether an item exists in a variable.

```sh
false
```

9. Run `keys(var.dict)` to convert a map `var "dict" {"us-east-1"="ami-1", "eu-west-1"="ami-2"}` to a list.

```sh
[
  "us-east-1",
  "eu-west-1"
]
```

10. Run `lookup(var.dict, "us-east-2", "ami-100")` to find a value in a map using a key and default value.

```sh
"ami-100"
```

## 6.8. Using inline conditions in Terraform

This runbook should be performed by the DevSecOps.

1. Edit your `main.tf` file and add a resource block as follows:

```diff
+resource "random_password" "password-generator" {
+  length = var.length<8 ? 8 : var.length
+}
```

## 6.9. Defining Terraform input variables based on precedence

This runbook should be performed by the DevSecOps.

1. Create a `variables.tf` file for your variable blocks.

```tf
variable "filename" {
  type        = string
  description = Name of file
}
variable "separator" {
  default     = "."
}
```

2. To make use of variable blocks, you can use `var` prefix. For example, `var.separator`.

3. If you have not declare an input variable value, then `terraform apply` will prompt you for the value:

```sh
var.filename
  Enter a value: myfile.txt
```

4. You can declare input variable values in several way, with the highest precedence in the top row:

| Priority |              Declare Variable              |
|:--------:|:------------------------------------------:|
|    1     | `-var` or `-var-file` (command-line flags) |
|    2     |    `*.auto.tfvars` (alphabetical order)    |
|    3     |             `terraform.tfvars`             |
|    4     | `TF_VAR_variable` (environment variables)  |

For example, `terraform apply -var "filename=yourfile.txt"` will overwrite values set through environment variables.

5. You can use any name for a variable except for:
  - `source`
  - `version`
  - `providers`
  - `count`
  - `for_each`
  - `lifecycle`
  - `depends_on`
  - `locals`

6. You cannot use other variables within your input variable (use locals instead). For example:

```diff
variable "instance_name" {
  type        = string
- default     = "${var.project_name}-server"
}
```

## 6.10. Using a validation block within a variable block

This runbook should be performed by the DevSecOps.

1. You can add a validation block within your variable block to constraint its values. For example:

```tf
variable "ami" {
  type        = string
  description = "The id of the Amazon Machine Image (AMI) to use for the instance."
  validation {
    condition     = substr(var.ami, 0, 4) == "ami-"
    error_message = "The AMI should start with \"ami-\"."
  }
}
```

## 6.11. Defining a Terraform input variable type

This runbook should be performed by the DevSecOps.

1. You can optionally define a type for your variable block, otherwise the default type is `any`.

|    Type    |      Reference       |                        Example                         |
|:----------:|:--------------------:|:------------------------------------------------------:|
|   string   |   var.instancetype   |                       "t2.micro"                       |
|   number   |   var.maxinstance    |                           2                            |
|    bool    |    var.isinstance    |                          true                          |
| list(type) |    var.server[0]     |                    ["web1", "web2"]                    |
| map(type)  |  var.region["dev"]   |        { "dev"="us-east-1", "sit"="us-west-2" }        |
|   tuple    |      var.web[0]      |                   ["web1", 3, true]                    |
|   object   | var.myobj["food"][0] | { "name"="bella", "food"=["fish","chicken","turkey"] } |

## 6.12. Defining a Terraform output variable

This runbook should be performed by the DevSecOps.

1. You can define a Terraform output variable using an output block with a required value.

```tf
output "public_ip" {
  value       = aws_instance.db_jumphost.public_ip
  description = "The Public IPv4 address of DB Jumphost"
}
```

2. You can run `terraform output [variable]` to display a list of outputs or a specific variable.

## 6.13. Creating an implicit or explicit dependency for a resource

This runbook should be performed by the DevSecOps.

1. You can create an implicit dependency from one resource to another in Terraform as follows:

```diff
resource "aws_instance" "cerberus" {
  ami           = var.ami
  instance_type = var.instance_type
+ key_name      = aws_key_pair.alpha.key_name
}

resource "aws_key_pair" "alpha" {
  key_name      = "alpha"
  public_key    = var.public_key
}
```

2. The `aws_key_pair` will be created before the `aws_instance` during apply, while `terraform destroy` will delete the instance first before the key.

3. You can create an explicit dependency with `depends_on` as follows:

```diff
resource "aws_instance" "db" {
  ami           = var.db_ami
  instance_type = var.db_instance_type
}

resource "aws_instance" "web" {
  ami           = var.web_ami
  instance_type = var_web_instance_type
+ depends_on    = [
+   aws_instance.db
+ ]
}
```

4. The db instance will be created before the web instance during apply, while `terraform destroy` will delete the web first before the db instance.

## 6.14. Referencing an existing resource using Terraform data block

This runbook should be performed by the DevSecOps.

1. You can define a Terraform data block to reference an existing resource that is not managed by Terraform. For example, you can identify the key with `key_name` attribute:

```tf
data "aws_key_pair" "cerberus-key" {
  key_name      = "alpha"
}
```

2. Alternatively, you can identify a resource using a `filter` block.

```diff
data "aws_key_pair" "cerberus-key" {
+ filter {
+   name        = "tag:project"
+   values      = ["cerberus"]
+ }
}
```

3. You can reference any exported attribute from data with `data.aws_key_pair.cerberus-key.attribute`.

## 6.15. Using a locals block for DRY strategy

This runbook should be performed by the DevSecOps.

1. You can add a `locals` block to define a reusable block. For example:

```tf
locals {
  common_tags = {
    Department  = "finance"
    Project     = "cerberus"
  }
}

resource "aws_instance" "db" {
  ...
  tags          = local.common_tag
}
```

## 6.16. Using a dynamic block for DRY strategy

This runbook should be performed by the DevSecOps.

1. You can add a `dynamic` block together with a list variable to define multiple blocks of the same attribute. For example:

```diff
variable "ingress_ports" {
  type          = list
  default       = [22, 8080]
}

resource "aws_security_group" "backend-sg" {
  name          = "backend-sg"
  vpc_id        = aws_vpc.backend-vpc.id
+ dynamic "ingress" {
+   for_each      = var.ingress_ports
+   content {
+     from_port   = ingress.value
+     to_port     = ingress.value
+     protocol    = "tcp"
+     cidr_blocks = ["0.0.0.0/0"]
+   }
+ }
}
```

2. You can optionally replace the default iterator within the dynamic block as follows:

```diff
resource "aws_security_group" "backend-sg" {
  ...
  dynamic "ingress" {
    for_each      = var.ingress_ports
+   iterator      = port
    content {
-     from_port   = ingress.value
-     to_port     = ingress.value
+     from_port   = port.value
+     to_port     = port.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

3. For the output block, you can use the splat (*) operator to iterate over the dynamic blocks as follows:

```tf
output "to_ports" {
  value           = aws_security_group.backend-sg.ingress[*].to_port
}
```

```sh
$ terraform output
to_ports = [
  22,
  8080
]
```

## 6.17. Creating a module

This runbook should be performed by the DevSecOps. Modules are the main way to package and reuse resource configurations with Terraform.

Re-usable modules are defined using all the same configuration concepts we use in root modules.
  * **Input variables** to accept values from the calling module.
  * **Output variables** to return results to the calling module.
  * **Resources** to define one or more infrastructure objects that the module will manage.

1. Create a new directory and place one or more `.tf` files. We recommend keeping the module tree relatively flat.
  * **Root module**: This is the only required element for the standard module structure. This should be the primary entrypoint for the module and is expected to be opinionated.
  * **README**: The root module and any nested modules should have `README` files.
  * **LICENSE**: The license under which the module is available.
  * **`main.tf`**, **`variables.tf`**, **`outputs.tf`**: These are recommended files for a minimal module, even if they're empty. `main.tf` should be the primary entrypoint.
  * **Variables and outputs should have description**: This is used for documentation that will be automatically generated.
  * **Nested modules**: These should exist under the `modules/` subdirectory. Any nested module with a `README` is considered reusable by an external user, otherwise if it doesn't exist, it is considered for internal use only. If the root module includes calls to nested modules, they should use relative paths like `./modules/`.
  * **Examples**: These should exist under the `examples/` subdirectory. Each example may have a `README` to explain the goal and usage of the example. Examples for submodules should also be placed in the root `examples/` subdirectory.

2. Create a minimal standard module structure as follows. We recommend this minimal standard module structure to keep the module tree flat, with only one level of modules (calling module + minimal module).

```sh
tree minimal-module/
.
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
```

3. Create a complete standard module structure as follows.

```sh
tree complete-module/
.
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
├── ...
├── modules/
│   ├── nestedA/
│   │   ├── README.md
│   │   ├── variables.tf
│   │   ├── main.tf
│   │   ├── outputs.tf
│   ├── nestedB/
│   ├── .../
├── examples/
│   ├── exampleA/
│   │   ├── main.tf
│   ├── exampleB/
│   ├── .../
```

4. Providers can be passed down to descendent modules in two ways:
  * **Implicitly** through inheritance. This is specified using a `required_providers` block inside a `terraform` block. Each module must declare its own provider requirements so that Terraform can ensure that there is a single version of the provider that is compatible with all modules in the configuration.
  * **Explicitly** via the `providers` argument within a `module` block. A reusable module containing its own provider configurations is not compatible with the `for_each`, `count`, and `depends_on` arguments.

---
# 7. Maintenance and Updates
## 7.1. Managing a Terraform state file

This runbook should be performed by the DevSecOps.

1. Run `terraform state list` to show all resources in your Terraform state file. For example:

```sh
local_file.classics
local_file.hall_of_fame
local_file.new_shows
local_file.top10
```

2. Run `terraform state show <RESOURCE>` to describe the resource in details.

3. Run `terraform state mv <SOURCE> <DESTINATION>` to rename a resource within a state file, or to move a resource to another state file.

4. Run `terraform state pull` to fetch and output a backend state file.

5. Run `terraform state rm <RESOURCE>` to remove a resource from your state file.

Note: Managing a Terraform state file does not change the real world infrastructure that was provisioned.

## 7.2. Tainting a Resource to replace it

This runbook should be performed by the DevSecOps.

Whenever `terraform apply` fails to create a resource, it will be marked as tainted. A tainted resource will be destroyed and replaced with a new instance. In some cases, you may want to mark a resource as tainted manually.

1. Run `terraform taint <RESOURCE>` to mark a resource as tainted.

2. Run `terraform plan` and `terraform apply` to replace the above resource.

3. Run `terraform untaint <RESOURCE>` to unmark a tainted resource.

## 7.3. Checking Terraform logs

This runbook should be performed by the DevSecOps.

1. Use the environment variable `TF_LOG` to set a log level [`INFO`,`WARNING`,`ERROR`,`DEBUG`,`TRACE`, `JSON`]. For example:

```sh
export TF_LOG=TRACE
```

2. To persist the logs in a file, use the environment variable `TF_LOG_PATH`. For example:

```sh
export TF_LOG_PATH=/tmp/terraform.log
```

3. To stop persisting the logs in a file, type `unset TF_LOG_PATH`.

## 7.4. Importing a real resource into Terraform

This runbook should be performed by the DevSecOps.

In some cases, you may want to import a real resource into Terraform. However, `terraform import` only adds a resource to your Terraform state file, but not to your `main.tf`.

1. Edit your `main.tf` file and add an empty resource block as follows:

```tf
resource <RESOURCE_TYPE> <RESOURCE_NAME> {
  # no arguments required
}
```

2. Run `terraform import <RESOURCE_TYPE>.<RESOURCE_NAME> <RESOURCE_ID>` to add a real resource to your Terraform state file.

3. Run `terraform show` to inspect the resource, and manually add the attributes to your resource block in `main.tf`.

```diff
resource <RESOURCE_TYPE> <RESOURCE_NAME> {
+ <ATTRIBUTE> = ""
}
```

4. Run `terraform plan` to iteratively to refresh the Terraform state file, and add any missing attributes until no changes. The resource will be managed by Terraform going forward.

---
## 7.5. Using Terraform modules from a public registry

This runbook should be performed by the DevSecOps.

Terraform modules in the public registry can be categorized as either `verified` or `community`.
- Modules with a `verified` blue tick are maintained and validated by HashiCorp.
- Modules with a `community` without a blue tick are maintained by the open source community, and not validated by HashiCorp.
- The syntax for specifying a registry module is `<NAMESPACE>/<NAME>/<PROVIDER>`, for example, `hashicorp/consul/aws`.

You can also use modules from a private registry, which has a source string like the public registry, but with an added hostname prefix.
- The syntax for specifying a private registry module is `<HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER>`.
- You may also need to configure credentials to access modules in a private registry.

1. Create a `variables.tf` file and add the following code:

```diff
+variable "cloud_users" {
+     type = string
+     default = "andrew:ken:faraz:mutsumi:peter:steve:braja"
+}
```

2. Create a `main.tf` file and add the following code

```diff
+module "cloud" {
+  source  = "terraform-aws-modules/iam/aws//modules/iam-user"
+  version = "5.39.1"
+  # insert the required variable here
+  count   = length(split(":", var.cloud_users))
+  name    = split(":", var.cloud_users)[count.index]
+  create_iam_access_key         = false
+  create_iam_user_login_profile = false
+}
```

3. Run `terraform init` to download the `terraform-aws-modules/iam` in your Terraform configuration file.

## 7.6. Managing multiple environments using Terraform Workspace

This runbook should be performed by the DevSecOps.

1. Run `terraform workspace new dev` to create a new workspace in your Terraform configuration.

Workspaces isolate their states within the same Terraform configuration project under a subfolder `terraform.tfstate.d`.

```sh
terraform.tfstate.d/
+- dev/
   |- terraform.tfstate
```

2. Run `terraform workspace list` to display a list of workspaces.

```sh
default
* dev
```

3. You can use `terraform.workspace` to reference the current workspace. For example,

```diff
resource "aws_instance" "web_server" {
  # other variables
  tags = {
+    Environment = terraform.workspace
  }
}
```

## 7.7. Targeting a resource during Terraform apply

This runbook should be performed by the DevSecOps.

1. You can specify the argument `-target` to update only a specific resource. Terraform apply will not update any explicit or implicit dependencies when using this argument. For example:

```sh
terraform apply -target random_string.server-suffix
```

Note: Use the `-target` argument rarely.

## 7.8. Publishing a public module

This runbook should be performed by the DevSecOps. The list below contains all the requirements for publishing a public module.
  * **GitHub**: The module must be on a GitHub public repo.
  * **Named `terraform-<PROVIDER>-<NAME>`**: The syntax must be a three-part format, where:
    * `PROVIDER`: the main provider where it creates that infrastructure, e.g. `aws`.
    * `NAME`: the type of infrastructure the module manages, e.g. `s3`.
  * **Description**: The module description is populated using the GitHub public repo description.
  * **Standard module structure**: The module must use a standard file and directory layout.
  * **`x.y.z` tags for releases**: Release tag names must be a semantic version, which can optionally be prefixed with a `v`, for example, `v1.0.4` and `1.0.4`.

1. Navigate to the Terraform Registry, and click the **Upload** link.
2. If you're not signed in, this will ask you to connect with GitHub, and ask for access to public repositories. An email address is required so that you will receive alerts about your module.
3. Select the repository of the module you want to add, and click **Publish Module**.
4. The Terraform Registry uses GitHub tags to detect releases. To release a new version, create and push a new tag, and a webhook will notify the registry of the new version.
5. If your new version doesn't appear, you may force a sync with GitHub by viewing your module on the registry and clicking **Resync Module** under the Manage Module dropdown.

---
# 8. References

The following resources were used as a single-use reference.

|                              Title                              | Type |       Author        |
|:---------------------------------------------------------------:|:----:|:-------------------:|
|               [My First Terraform Provider][r01]                | Web  |     Marc Falzon     |
|    [Beginner's guide to creating a Terraform Provider][r02]     | Web  |   Mark McDonnell    |
|      [How to Develop a Custom Provider in Terraform][r03]       | Web  | Saravanan Gnanaguru |
| [Implement a provider with the Terraform Plugin Framework][r04] | Web  |      HashiCorp      |
|                   [Terraform Providers][r05]                    | Web  |      HashiCorp      |
|                     [GitHub Provider][r06]                      | Web  |      HashiCorp      |
|             [Configuration Language > Modules][r07]             | Web  |      HashiCorp      |
|     [Registry Publishing > Finding and Using Modules][r08]      | Web  |      HashiCorp      |
|                  [Backend block > remote][r09]                  | Web  |      HashiCorp      |

[r01]: https://blog.revolve.team/2018/02/23/my-first-terraform-provider
[r02]: https://www.integralist.co.uk/posts/terraform-provider
[r03]: https://www.infracloud.io/blogs/developing-terraform-custom-provider
[r04]: https://developer.hashicorp.com/terraform/tutorials/providers-plugin-framework/providers-plugin-framework-provider
[r05]: https://registry.terraform.io/browse/providers
[r06]: https://registry.terraform.io/providers/integrations/github/latest/docs
[r07]: https://developer.hashicorp.com/terraform/language/modules
[r08]: https://developer.hashicorp.com/terraform/registry/modules/use
[r09]: https://developer.hashicorp.com/terraform/language/backend/remote