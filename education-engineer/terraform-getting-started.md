# Getting Started with Terraform

Terraform is an infrastructure as code (IaC) tool for building, changing and managing infrastructure on multiple cloud platforms.

This tutorial will guide you through the steps to install and use Terraform to deploy an NGINX container running in Docker on your local computer.

## Prerequisites

- A personal computer (Mac, Windows or Linux).
- [Docker](https://docs.docker.com/engine/install/) Desktop (Mac/Windows) or Docker Engine (Linux).
- A text editor.


## Install Terraform

To install Terraform, open a web browser and go to [Terraform.io](https://www.terraform.io/downloads.html). Select your operating system and architecture. Choose a method for the installation, Hashicorp distributes Terraform in a single binary package. You can also use package managers for the installation.


## Verify the installation

Once the terraform binary is available on your system, open a terminal session and run the `terraform -help` command:

```shell
$ terraform -help
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.
The primary workflow commands are given first, followed by
less common or more advanced commands.
...
```

If you don't see the output above, please review the installation instructions for your operating system.

## Create a Terraform project

With the Terraform binary installed on your system, you can now start to deploy infrastructure.

Continue working in your terminal session or open a new one. Create a directory named `terraform-demo` and navigate into it. You will create your Terraform configuration file inside this directory.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create an empty file for your Terraform code and name it `main.tf`.

```shell
$ touch main.tf
```

Use your favorite text editor to open the `main.tf` file and paste the following lines of Terraform code:

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.20.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "tutorial"
  ports {
    internal = 80
    external = 8000
  }
}
```

This sample code is written in the [Terraform Language](https://www.terraform.io/language), which is used to declare [resources](https://www.terraform.io/language/resources). Once executed, it will perform the following actions on your computer:

- Download and install the `docker` Terraform [provider](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs).
- Download the `nginx` Docker [image](https://hub.docker.com/_/nginx).
- Create and run the `tutorial` Docker container.

Initialize Terraform with the `terraform init` command:

```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
...
Terraform has been successfully initialized!
...
```

This command creates the `.terraform` hidden directory and some state and lock files under the `terraform-demo` directory.

With Terraform initialized, you can now run `terraform plan` to see any changes that are needed to deploy your NGINX container.

```shell
$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
...
Plan: 2 to add, 0 to change, 0 to destroy.
```

You shoud check for any errors. If the command ran successfully, provision the resources with the `terraform apply` command. Type `yes` at the prompt and hit ENTER.

```shell
$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
...
Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes
```

The command will take a few seconds to run and will display a message indicating that the resources were created.

```shell
...
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

To check that the `nginx` image was successfully downloaded, you can run the `docker image ls` command:

```shell
docker image ls
REPOSITORY                 TAG                   IMAGE ID       CREATED         SIZE
nginx                      latest                b692a91e4e15   2 weeks ago     142MB
```

To check that the `tutorial` container is running, you can run the `docker container ls` command:

```shell
docker container ls
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
437ccd133b88   b692a91e4e15   "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:8000->80/tcp   tutorial
```

You can verify that you can access the NGINX container content by opening a web browser and typing `localhost:8000` in the address bar or by running the `curl` command on the terminal:

```shell
$ curl localhost:8000
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
```

To remove the `tutorial` container and the `nginx` image, run the `terraform destroy` command. Type `yes` at the prompt and hit ENTER. Terraform will destroy the resources it had created in the previous step:

```shell
$ terraform destroy

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy
...
Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

```

Congratulations! You have successfully installed and run Terraform to deploy infrastructure.

## Next steps

In this tutorial, you have deployed infrastructure resources on your local computer using the `terraform` command.

Now you are ready to start building and deploying resources in public clouds such as [AWS](https://aws.amazon.com/), [GCP](https://cloud.google.com/) or [Azure](https://azure.microsoft.com/) using the available Terraform [providers](https://registry.terraform.io/browse/providers).

## Additional resources
- [Terraform repository](https://github.com/hashicorp/terraform)
- [Terraform website](https://www.terraform.io/)
- [Terraform documentation](https://www.terraform.io/docs/)
- [Hashicorp Learn](https://learn.hashicorp.com/terraform)
