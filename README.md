# Product-service-CI-CD

This repository contains a sample webapp project developed using [java spring boot](https://spring.io/projects/spring-boot).

The aim of this repository is to show some examples of github workflows that interact with [terraform](https://developer.hashicorp.com/terraform), [ansible](https://docs.ansible.com/) and [docker](https://www.docker.com/) in order to automate the application deployment.

You can find the following workflows:
* `infrastructure-workflow.yml`: creates and sets up a new AWS EC2 instance for deployment operations.
* `deploy-workflow.yml`: uses GitHub actions to compile the project, and Ansible to set up the EC2 instance and start the application.
* `docker-workflow.yml`: uses GitHub actions and Ansible to set up the EC2 instance installing docker and starting containers defined int the `docker-compose.yml` file.

>[!NOTE]
>Some workflow runs on `push`. In this case you have to manually trigger the workflows going to the `Actions` section of this repository an then press the `Run workflow` button.

## Set up repository

>[!WARNING]
> DO NOT try to modify secrets, commit or push file on this repository (you are not allowed to do something like this). Fork this repository and make there all your experiments.

If you want to run the workflows in this repository you need first to set up some secret variables.

>[!TIP]
> A **secret** is a variable used to store sensitive information like API key, token and password. You can read more about GitHub secrets [here](https://docs.github.com/en/actions/concepts/security/secrets).
>
>To set up a secret go to repository settings > security > secret and variables > actions.


Follow the next steps to understand and setup the secret you need to run github workflows.

### Get AWS access key

> Required to run [infrastructure-workflow.yml](.github/workflows/infrastructure-workflow.yml)

AWS access keys are long-term credentials for root and IAM users and allows them to sign programmatic requests to AWS CLI or AWS API. You can read more about AWS access keys [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html).

When you want to use terraform for generating a new cloud infrastructure you will need AWS access keys such as:
* An **AWS access key ID**: public user identifier. Same functions as username.
* An **AWS secret access key**: private user key. Same function as password. Needs to be carefully protected.

You can obtain AWS access keys using your AWS console and following the steps shown [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user_manage_add-key.html).

Copy and paste your generated credentials into two respectively secret variables named `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

### Set up a PAT token

> Required to run [infrastructure-workflow.yml](.github/workflows/infrastructure-workflow.yml)

Personal access tokens are an alternative to using passwords for authentication to GitHub when using the GitHub API or the command line. You can read more about GitHub tokens [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens).

If you want to automatically create/update the `EC2_HOST` secret with your EC2 instance IP after the infrastucture creation, **you need a PAT token**.

You can obtain the token following the steps shown [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic).

Copy and paste your generated token into a secret variable named `PAT_TOKEN`.

### Generate the SSH deploy key

> Required to run
> [infrastructure-workflow.yml](.github/workflows/infrastructure-workflow.yml),
> [deploy-workflow.yml](.github/workflows/deploy-workflow.yml), 
> [docker-workflow.yml](.github/workflows/docker-workflow.yml)

**SSH keypair** is a cryptographic keypair used for secure authentications in SSH connections. You can read more about SSH keypair [here](https://www.ssh.com/academy/ssh/public-key-authentication).

When you want to communicate with an AWS EC2 instance you need an SSH keypair and you need set up your instance to accept conncetions with your public key.

First generate an SSH keypair using:

```bash
cd ~/.ssh
ssh-keygen -t ed25519 -C "deploy_key" -f ./deploy_key -N ""
```

You can view key values using:

```bash
cd ~/.ssh
sudo cat deploy_key.pub     # Prints your public key
sudo cat deploy_key         # Prints your private key
```

Copy and paste all the content of your generated `deploy_key.pub` into the `.ssh` folder into **this repository**.

Copy and paste all the content of your generated `deploy_key` into the `EC2_SSH_KEY` secret.