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

|            Category            |              Activity               | Developer | DevSecOps | Manager |
|:------------------------------:|:-----------------------------------:|:---------:|:---------:|:-------:|
| Installation and Configuration | Set up your development environment |    R,A    |           |         |
| Installation and Configuration |  Installing Terraform with `tfenv`  |           |    R,A    |         |

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

---
# References

The following resources were used as a single-use reference.

|                                                                                     Title                                                                                      | Type |  Author   |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:----:|:---------:|
| [How to Develop a Custom Provider in Terraform](https://www.infracloud.io/blogs/developing-terraform-custom-provider) | Web | Saravanan Gnanaguru |
| [Implement a provider with the Terraform Plugin Framework](https://developer.hashicorp.com/terraform/tutorials/providers-plugin-framework/providers-plugin-framework-provider) | Web  | HashiCorp |
|                                                     [Terraform Providers](https://registry.terraform.io/browse/providers)                                                      | Web  | HashiCorp |
| [GitHub Provider](https://registry.terraform.io/providers/integrations/github/latest/docs) | Web | HashiCorp |