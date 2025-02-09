---
title: Container Pipeline Exercise
weight: 49
---

The AWS Resources for pipeline, etc are build using Terraform. 

1. Create the Terraform state bucket 
2. Initialize Terraform
3. Plan Terraform 
4. Apply terraform

## First Create the state file s3 bucket 

`aws s3 mb s3://$NAME_S3_BUCKET --region us-west-2`

```bash
make_bucket: $NAME_S3_BUCKET
```

{{% notice warning %}}
Ensure the s3 bucket is updates and is unique. 
{{% /notice %}}

Inside our Terraform Directory in the devsecops repo `cd ~/environments/devsecopspipeline/`

Update the `~/environments/devsecopspipeline/terraform/config.tf` with the bucket name to store terraform state 

```hcl 
terraform {
    backend "s3" {
        bucket = "$NAME_S3_BUCKET"
        key    = "$NAME_S3_BUCKET/terraform_state"
        region = "us-west-2"
    }
}
```

## Initialize the Terraform 

`cd ~/environment/devsecopspipeline/`

Use make to Initialize, this is the first step of the Terraform execution. 

`make tf_clean tf_init`

{{% expand%}}
```bash
cd terraform/ && \
rm -rf .terraform \
rm -rf plan.out
cd terraform/ && \
terraform init
Initializing the backend...

Successfully configured the backend "s3"! Terraform will automatically
use this backend unless the backend configuration changes.

Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 3.0"...
- Installing hashicorp/aws v3.58.0...
- Installed hashicorp/aws v3.58.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```
{{% /expand%}}

## Terraform Plan  

Terraform Plan will create the plan for the run and allow us to see all the resources that will be created. 

`make tf_plan`

{{%expand "Expand here is what it looks like in its entirety" %}}
```bash

cd terraform/ && \
terraform plan -out=plan.out
aws_iam_policy.allow-eks-asg: Refreshing state... [id=arn:aws:iam::123456789012:policy/eks-devsecops-allow-eks-asg]
aws_s3_bucket.codepipeline_bucket: Refreshing state... [id=houston-devsecops-code]
aws_ecr_repository.golang_example: Refreshing state... [id=golang_example-houston]
aws_codecommit_repository.devsecops: Refreshing state... [id=houston-devsecops-repo]
aws_iam_role.codepipeline_role: Refreshing state... [id=houston-test-role]
aws_iam_role.main: Refreshing state... [id=eks-devsecops]
aws_iam_role.devsecops-austin-codebuild: Refreshing state... [id=devsecops-houston-codebuild]
aws_cloudwatch_log_group.codebuild: Refreshing state... [id=devsecops-houston-codebuild]
aws_s3_bucket.codebuild_s3: Refreshing state... [id=devsecops-houston-codebuild]
aws_cloudwatch_log_stream.codebuild: Refreshing state... [id=devsecops-houston-codebuild]
aws_ssm_parameter.account_id: Refreshing state... [id=ACCOUNT_ID]
aws_codebuild_project.devsecops-austin-codebuild-BUILD: Refreshing state... [id=arn:aws:codebuild:us-west-2:123456789012:project/devsecops-houston-codebuild-BUILD]
aws_iam_role_policy_attachment.AmazonEKSClusterPolicy: Refreshing state... [id=devsecops-houston-codebuild-20210911231810453900000003]
aws_iam_role_policy_attachment.AmazonEKSServicePolicy: Refreshing state... [id=devsecops-houston-codebuild-20210911231810424800000002]
aws_iam_role_policy.devsecops-austin-codebuild: Refreshing state... [id=devsecops-houston-codebuild:terraform-20210911231810411800000001]
aws_codebuild_project.devsecops-austin-codebuild-DEPLOY: Refreshing state... [id=arn:aws:codebuild:us-west-2:123456789012:project/devsecops-houston-codebuild-DEPLOY]
aws_codebuild_project.devsecops-austin-codebuild-STATIC: Refreshing state... [id=arn:aws:codebuild:us-west-2:123456789012:project/devsecops-houston-codebuild-STATIC]
aws_iam_role_policy.codepipeline_policy: Refreshing state... [id=houston-test-role:houston-codepipeline_policy]
aws_codepipeline.codepipeline: Refreshing state... [id=houston-devsecops-pipeline]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":

  # aws_iam_role.main has been changed
  ~ resource "aws_iam_role" "main" {
        id                    = "eks-devsecops"
        name                  = "eks-devsecops"
      + tags                  = {}
        # (10 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }
  # aws_codepipeline.codepipeline has been changed
  ~ resource "aws_codepipeline" "codepipeline" {
        id       = "houston-devsecops-pipeline"
        name     = "houston-devsecops-pipeline"
      + tags     = {}
        # (3 unchanged attributes hidden)


      ~ stage {
            name = "Source"

          ~ action {
              + input_artifacts  = []
                name             = "Source"
                # (7 unchanged attributes hidden)
            }
        }
      ~ stage {
            name = "Build"

          ~ action {
                name             = "Build"
              + output_artifacts = []
                # (7 unchanged attributes hidden)
            }
        }
      ~ stage {
            name = "Deploy"

          ~ action {
                name             = "Deploy"
              + output_artifacts = []
                # (7 unchanged attributes hidden)
            }
        }
        # (2 unchanged blocks hidden)
    }
  # aws_s3_bucket.codepipeline_bucket has been changed
  ~ resource "aws_s3_bucket" "codepipeline_bucket" {
        id                          = "houston-devsecops-code"
      + tags                        = {}
        # (10 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }
  # aws_ssm_parameter.account_id has been changed
  ~ resource "aws_ssm_parameter" "account_id" {
        id        = "ACCOUNT_ID"
        name      = "ACCOUNT_ID"
      + tags      = {}
        # (8 unchanged attributes hidden)
    }
  # aws_codecommit_repository.devsecops has been changed
  ~ resource "aws_codecommit_repository" "devsecops" {
        id              = "houston-devsecops-repo"
      + tags            = {}
        # (8 unchanged attributes hidden)
    }
  # aws_cloudwatch_log_group.codebuild has been changed
  ~ resource "aws_cloudwatch_log_group" "codebuild" {
        id                = "devsecops-houston-codebuild"
        name              = "devsecops-houston-codebuild"
      + tags              = {}
        # (3 unchanged attributes hidden)
    }
  # aws_iam_role.codepipeline_role has been changed
  ~ resource "aws_iam_role" "codepipeline_role" {
        id                    = "houston-test-role"
        name                  = "houston-test-role"
      + tags                  = {}
        # (9 unchanged attributes hidden)

      - inline_policy {}
      + inline_policy {
          + name   = "houston-codepipeline_policy"
          + policy = jsonencode(
                {
                  + Statement = [
                      + {
                          + Action   = [
                              + "s3:GetObject",
                              + "s3:GetObjectVersion",
                              + "s3:GetBucketVersioning",
                              + "s3:PutObject",
                            ]
                          + Effect   = "Allow"
                          + Resource = [
                              + "arn:aws:s3:::houston-devsecops-code",
                              + "arn:aws:s3:::houston-devsecops-code/*",
                            ]
                        },
                      + {
                          + Action   = [
                              + "codebuild:BatchGetBuilds",
                              + "codebuild:StartBuild",
                            ]
                          + Effect   = "Allow"
                          + Resource = "*"
                        },
                    ]
                  + Version   = "2012-10-17"
                }
            )
        }
    }
  # aws_s3_bucket.codebuild_s3 has been changed
  ~ resource "aws_s3_bucket" "codebuild_s3" {
        id                          = "devsecops-houston-codebuild"
      + tags                        = {}
        # (10 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }
  # aws_iam_role.devsecops-austin-codebuild has been changed
  ~ resource "aws_iam_role" "devsecops-austin-codebuild" {
        id                    = "devsecops-houston-codebuild"
      ~ managed_policy_arns   = [
          + "arn:aws:iam::aws:policy/AmazonEKSClusterPolicy",
          + "arn:aws:iam::aws:policy/AmazonEKSServicePolicy",
        ]
        name                  = "devsecops-houston-codebuild"
      + tags                  = {}
        # (8 unchanged attributes hidden)

      - inline_policy {}
      + inline_policy {
          + name   = "terraform-20210911231810411800000001"
          + policy = jsonencode(
                {
                  + Statement = [
                      + {
                          + Action   = [
                              + "logs:CreateLogGroup",
                              + "logs:CreateLogStream",
                              + "logs:PutLogEvents",
                              + "sts:AssumeRole",
                              + "eks:*",
                            ]
                          + Effect   = "Allow"
                          + Resource = [
                              + "*",
                            ]
                        },
                      + {
                          + Action   = [
                              + "ec2:CreateNetworkInterface",
                              + "ec2:DescribeDhcpOptions",
                              + "ec2:DescribeNetworkInterfaces",
                              + "ec2:DeleteNetworkInterface",
                              + "ec2:DescribeSubnets",
                              + "ec2:DescribeSecurityGroups",
                              + "ec2:DescribeVpcs",
                              + "ecr:*",
                              + "ssm:*",
                            ]
                          + Effect   = "Allow"
                          + Resource = "*"
                        },
                      + {
                          + Action   = [
                              + "ec2:CreateNetworkInterfacePermission",
                            ]
                          + Effect   = "Allow"
                          + Resource = [
                              + "arn:aws:ec2:us-west-2:*:network-interface/*",
                            ]
                        },
                      + {
                          + Action   = [
                              + "s3:*",
                            ]
                          + Effect   = "Allow"
                          + Resource = [
                              + "arn:aws:s3:::devsecops-houston-codebuild",
                              + "arn:aws:s3:::devsecops-houston-codebuild/*",
                              + "arn:aws:s3:::houston-devsecops-code",
                              + "arn:aws:s3:::houston-devsecops-code/*",
                            ]
                        },
                    ]
                  + Version   = "2012-10-17"
                }
            )
        }
    }
  # aws_iam_policy.allow-eks-asg has been changed
  ~ resource "aws_iam_policy" "allow-eks-asg" {
        id        = "arn:aws:iam::123456789012:policy/eks-devsecops-allow-eks-asg"
        name      = "eks-devsecops-allow-eks-asg"
      + tags      = {}
        # (5 unchanged attributes hidden)
    }

Unless you have made equivalent changes to your configuration, or ignored the relevant attributes using ignore_changes, the following plan may include actions to undo or respond to these changes.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

No changes. Your infrastructure matches the configuration.

Your configuration already matches the changes detected above. If you'd like to update the Terraform state to match, create and apply a refresh-only plan:
  terraform apply -refresh-only
```
{{% /expand%}}

## Terraform Apply

Terraform uses the selected providers to generate the following execution plan. Resource actions are indicated with 
the following symbols: ```+ create```

`make tf_apply`

{{%expand "Expand here is what it looks like in its entirety" %}}
```bash
cd terraform/ && \
terraform apply -auto-approve

Terraform will perform the following actions:

# aws_cloudwatch_log_group.codebuild will be created
+ resource "aws_cloudwatch_log_group" "codebuild" {
    + arn               = (known after apply)
    + id                = (known after apply)
    + name              = "devsecops-houston-codebuild"
    + retention_in_days = 0
    + tags_all          = (known after apply)
      }

# aws_cloudwatch_log_stream.codebuild will be created
+ resource "aws_cloudwatch_log_stream" "codebuild" {
    + arn            = (known after apply)
    + id             = (known after apply)
    + log_group_name = "devsecops-houston-codebuild"
    + name           = "devsecops-houston-codebuild"
      }

# aws_codebuild_project.devsecops-austin-codebuild-BUILD will be created
+ resource "aws_codebuild_project" "devsecops-austin-codebuild-BUILD" {
    + arn            = (known after apply)
    + badge_enabled  = false
    + badge_url      = (known after apply)
    + build_timeout  = 5
    + description    = "devsecops-houston-codebuild-BUILD"
    + encryption_key = (known after apply)
    + id             = (known after apply)
    + name           = "devsecops-houston-codebuild-BUILD"
    + queued_timeout = 480
    + service_role   = (known after apply)
    + source_version = "master"
    + tags           = {
        + "Environment" = "devsecops-houston"
          }
    + tags_all       = {
        + "Environment" = "devsecops-houston"
          }

    + artifacts {
        + encryption_disabled    = false
        + override_artifact_name = false
        + type                   = "CODEPIPELINE"
          }

    + environment {
        + compute_type                = "BUILD_GENERAL1_LARGE"
        + image                       = "aws/codebuild/standard:2.0"
        + image_pull_credentials_type = "CODEBUILD"
        + privileged_mode             = true
        + type                        = "LINUX_CONTAINER"

        + environment_variable {
            + name  = "ACCOUNT_ID"
            + type  = "PARAMETER_STORE"
            + value = "ACCOUNT_ID"
              }
        + environment_variable {
            + name  = "EKS_KUBECTL_ROLE_NAME"
            + type  = "PLAINTEXT"
            + value = "devsecops-houston-codebuild"
              }
              }

    + logs_config {
        + cloudwatch_logs {
            + group_name  = "devsecops-houston-codebuild"
            + status      = "ENABLED"
            + stream_name = "devsecops-houston-codebuild"
              }

        + s3_logs {
            + encryption_disabled = false
            + location            = (known after apply)
            + status              = "ENABLED"
              }
              }

    + source {
        + buildspec = <<-EOT
          ---
          version: 0.2

              phases:
                install:
                  commands:
                    - apt-get update && apt-get -y install curl jq python3-pip python3-dev && pip3 install --upgrade awscli
                    - curl -sS -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-07-26/bin/linux/amd64/aws-iam-authenticator
                    - curl -sS -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl
                    - chmod +x ./kubectl ./aws-iam-authenticator
                    - export PATH=$PWD/:$PATH
              
                pre_build:
                  commands:
                    - TAG="$REPOSITORY_NAME.$REPOSITORY_BRANCH.$ENVIRONMENT_NAME.$(date +%Y-%m-%d.%H.%M.%S).$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | head -c 8)"
                    - sed -i 's@CONTAINER_IMAGE@'"$REPOSITORY_URI:$TAG"'@' app.yml
                    - CREDENTIALS=$(aws sts assume-role --role-arn arn:aws:iam::$ACCOUNT_ID:role/$EKS_KUBECTL_ROLE_NAME --role-session-name codebuild-kubectl --duration-seconds 900)
                    - export AWS_ACCESS_KEY_ID="$(echo ${CREDENTIALS} | jq -r '.Credentials.AccessKeyId')"
                    - export AWS_SECRET_ACCESS_KEY="$(echo ${CREDENTIALS} | jq -r '.Credentials.SecretAccessKey')"
                    - export AWS_SESSION_TOKEN="$(echo ${CREDENTIALS} | jq -r '.Credentials.SessionToken')"
                    - export AWS_EXPIRATION=$(echo ${CREDENTIALS} | jq -r '.Credentials.Expiration')
                    - $(aws ecr get-login --no-include-email)
                build:
                  commands:
                    - make docker_build
              
                post_build:
                  commands:
                    - CREDENTIALS=$(aws sts assume-role --role-arn arn:aws:iam::$ACCOUNT_ID:role/$EKS_KUBECTL_ROLE_NAME  --role-session-name codebuild-kubectl --duration-seconds 900)
                    - export AWS_ACCESS_KEY_ID="$(echo ${CREDENTIALS} | jq -r '.Credentials.AccessKeyId')"
                    - export AWS_SECRET_ACCESS_KEY="$(echo ${CREDENTIALS} | jq -r '.Credentials.SecretAccessKey')"
                    - export AWS_SESSION_TOKEN="$(echo ${CREDENTIALS} | jq -r '.Credentials.SessionToken')"
                    - export AWS_EXPIRATION=$(echo ${CREDENTIALS} | jq -r '.Credentials.Expiration')
                    - make docker_push
              artifacts:
                files: build.json
          EOT
        + type      = "CODEPIPELINE"
          }
          }

# aws_codebuild_project.devsecops-austin-codebuild-DEPLOY will be created
+ resource "aws_codebuild_project" "devsecops-austin-codebuild-DEPLOY" {
    + arn            = (known after apply)
    + badge_enabled  = false
    + badge_url      = (known after apply)
    + build_timeout  = 5
    + description    = "devsecops-houston-codebuild-DEPLOY"
    + encryption_key = (known after apply)
    + id             = (known after apply)
    + name           = "devsecops-houston-codebuild-DEPLOY"
    + queued_timeout = 480
    + service_role   = (known after apply)
    + source_version = "master"
    + tags           = {
        + "Environment" = "devsecops-houston"
          }
    + tags_all       = {
        + "Environment" = "devsecops-houston"
          }

    + artifacts {
        + encryption_disabled    = false
        + override_artifact_name = false
        + type                   = "CODEPIPELINE"
          }

    + environment {
        + compute_type                = "BUILD_GENERAL1_LARGE"
        + image                       = "aws/codebuild/standard:2.0"
        + image_pull_credentials_type = "CODEBUILD"
        + privileged_mode             = true
        + type                        = "LINUX_CONTAINER"

        + environment_variable {
            + name  = "ACCOUNT_ID"
            + type  = "PARAMETER_STORE"
            + value = "ACCOUNT_ID"
              }
        + environment_variable {
            + name  = "EKS_KUBECTL_ROLE_NAME"
            + type  = "PLAINTEXT"
            + value = "devsecops-houston-codebuild"
              }
              }

    + logs_config {
        + cloudwatch_logs {
            + group_name  = "devsecops-houston-codebuild"
            + status      = "ENABLED"
            + stream_name = "devsecops-houston-codebuild"
              }

        + s3_logs {
            + encryption_disabled = false
            + location            = (known after apply)
            + status              = "ENABLED"
              }
              }

    + source {
        + buildspec = <<-EOT
          ---
          version: 0.2
          phases:
          install:
          commands:
          - curl -sS -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-07-26/bin/linux/amd64/aws-iam-authenticator
          - curl -sS -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl
          - chmod +x ./kubectl ./aws-iam-authenticator
          - export PATH=$PWD/:$PATH
          - apt-get update && apt-get -y install jq python3-pip python3-dev && pip3 install --upgrade awscli
          pre_build:
          commands:
          - TAG="$REPOSITORY_NAME.$REPOSITORY_BRANCH.$ENVIRONMENT_NAME.$(date +%Y-%m-%d.%H.%M.%S).$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | head -c 8)"
          - sed -i 's@CONTAINER_IMAGE@'"$REPOSITORY_URI:$TAG"'@' app.yml
          - cat app.yml
          - $(aws ecr get-login --no-include-email)
          - export KUBECONFIG=$HOME/.kube/config
          build:
          commands:
          - CREDENTIALS=$(aws sts assume-role --role-arn arn:aws:iam::$ACCOUNT_ID:role/$EKS_KUBECTL_ROLE_NAME --role-session-name codebuild-kubectl --duration-seconds 900)
          - export AWS_ACCESS_KEY_ID="$(echo ${CREDENTIALS} | jq -r '.Credentials.AccessKeyId')"
          - export AWS_SECRET_ACCESS_KEY="$(echo ${CREDENTIALS} | jq -r '.Credentials.SecretAccessKey')"
          - export AWS_SESSION_TOKEN="$(echo ${CREDENTIALS} | jq -r '.Credentials.SessionToken')"
          - export AWS_EXPIRATION="$(echo ${CREDENTIALS} | jq -r '.Credentials.Expiration')"
          - echo "EKS Cluster Name ${EKS_CLUSTER_NAME}"
          - make kube_update
          - kubectl apply -f app.yml --kubeconfig /root/.kube/config
          post_build:
          commands:
          - kubectl get pods -l app=devsecopspipeline
          artifacts:
          files: build.json
          EOT
        + type      = "CODEPIPELINE"
          }
          }

# aws_codebuild_project.devsecops-austin-codebuild-STATIC will be created
+ resource "aws_codebuild_project" "devsecops-austin-codebuild-STATIC" {
    + arn            = (known after apply)
    + badge_enabled  = false
    + badge_url      = (known after apply)
    + build_timeout  = 5
    + description    = "devsecops-houston-codebuild-STATIC"
    + encryption_key = (known after apply)
    + id             = (known after apply)
    + name           = "devsecops-houston-codebuild-STATIC"
    + queued_timeout = 480
    + service_role   = (known after apply)
    + source_version = "master"
    + tags           = {
        + "Environment" = "devsecops-houston"
          }
    + tags_all       = {
        + "Environment" = "devsecops-houston"
          }

    + artifacts {
        + artifact_identifier    = "static-code-report"
        + encryption_disabled    = false
        + override_artifact_name = false
        + type                   = "CODEPIPELINE"
          }

    + environment {
        + compute_type                = "BUILD_GENERAL1_LARGE"
        + image                       = "aws/codebuild/standard:2.0"
        + image_pull_credentials_type = "CODEBUILD"
        + privileged_mode             = true
        + type                        = "LINUX_CONTAINER"

        + environment_variable {
            + name  = "ACCOUNT_ID"
            + type  = "PARAMETER_STORE"
            + value = "ACCOUNT_ID"
              }
        + environment_variable {
            + name  = "EKS_KUBECTL_ROLE_NAME"
            + type  = "PLAINTEXT"
            + value = "devsecops-houston-codebuild"
              }
              }

    + logs_config {
        + cloudwatch_logs {
            + group_name  = "devsecops-houston-codebuild"
            + status      = "ENABLED"
            + stream_name = "devsecops-houston-codebuild"
              }

        + s3_logs {
            + encryption_disabled = false
            + location            = (known after apply)
            + status              = "ENABLED"
              }
              }

    + source {
        + buildspec = <<-EOT
          ---
          version: 0.2

              phases:
                install:
                  commands:
                    - make go_version
                    - apt-get update && apt-get -y install curl jq
                    - curl -o go1.13.5.linux-amd64.tar.gz https://dl.google.com/go/go1.13.5.linux-amd64.tar.gz
                    - tar -C /usr/local -xzf go1.13.5.linux-amd64.tar.gz
                    - export PATH=${PATH}:/usr/local/go/bin
                    - export GOROOT=/usr/local/go
                    - go version
                build:
                  commands:
                    - export PATH=${PATH}:/usr/local/go/bin
                    - export GOROOT=/usr/local/go
                    - make go_report
                post_build:
                  commands:
                    - mv src-*.html devsecopspipeline.html
              artifacts:
                files:
                  - devsecopspipeline.html
                name: static-code-report
          EOT
        + type      = "CODEPIPELINE"
          }
          }

# aws_codecommit_repository.devsecops will be created
+ resource "aws_codecommit_repository" "devsecops" {
    + arn             = (known after apply)
    + clone_url_http  = (known after apply)
    + clone_url_ssh   = (known after apply)
    + default_branch  = "main"
    + description     = "This is the Sample App Repository for AWS DevSecOps Workshop"
    + id              = (known after apply)
    + repository_id   = (known after apply)
    + repository_name = "houston-devsecops-repo"
    + tags_all        = (known after apply)
      }

# aws_codepipeline.codepipeline will be created
+ resource "aws_codepipeline" "codepipeline" {
    + arn      = (known after apply)
    + id       = (known after apply)
    + name     = "houston-devsecops-pipeline"
    + role_arn = (known after apply)
    + tags_all = (known after apply)

    + artifact_store {
        + location = "houston-devsecops-code"
        + region   = (known after apply)
        + type     = "S3"
          }

    + stage {
        + name = "Source"

        + action {
            + category         = "Source"
            + configuration    = {
                + "BranchName"     = "main"
                + "RepositoryName" = "houston-devsecops-repo"
                  }
            + name             = "Source"
            + output_artifacts = [
                + "source_output",
                  ]
            + owner            = "AWS"
            + provider         = "CodeCommit"
            + region           = (known after apply)
            + run_order        = (known after apply)
            + version          = "1"
              }
              }
    + stage {
        + name = "Report"

        + action {
            + category         = "Test"
            + configuration    = {
                + "ProjectName" = "devsecops-houston-codebuild-STATIC"
                  }
            + input_artifacts  = [
                + "source_output",
                  ]
            + name             = "Report"
            + output_artifacts = [
                + "static_output",
                  ]
            + owner            = "AWS"
            + provider         = "CodeBuild"
            + region           = (known after apply)
            + run_order        = (known after apply)
            + version          = "1"
              }
              }
    + stage {
        + name = "Build"

        + action {
            + category        = "Build"
            + configuration   = {
                + "ProjectName" = "devsecops-houston-codebuild-BUILD"
                  }
            + input_artifacts = [
                + "source_output",
                  ]
            + name            = "Build"
            + owner           = "AWS"
            + provider        = "CodeBuild"
            + region          = (known after apply)
            + run_order       = (known after apply)
            + version         = "1"
              }
              }
    + stage {
        + name = "Deploy"

        + action {
            + category        = "Build"
            + configuration   = {
                + "ProjectName" = "devsecops-houston-codebuild-DEPLOY"
                  }
            + input_artifacts = [
                + "source_output",
                  ]
            + name            = "Deploy"
            + owner           = "AWS"
            + provider        = "CodeBuild"
            + region          = (known after apply)
            + run_order       = (known after apply)
            + version         = "1"
              }
              }
              }

# aws_ecr_repository.golang_example will be created
+ resource "aws_ecr_repository" "golang_example" {
    + arn                  = (known after apply)
    + id                   = (known after apply)
    + image_tag_mutability = "IMMUTABLE"
    + name                 = "golang_example-houston"
    + registry_id          = (known after apply)
    + repository_url       = (known after apply)
    + tags                 = {
        + "env" = "devsecops"
          }
    + tags_all             = {
        + "env" = "devsecops"
          }

    + image_scanning_configuration {
        + scan_on_push = true
          }
          }

# aws_iam_policy.allow-eks-asg will be created
+ resource "aws_iam_policy" "allow-eks-asg" {
    + arn       = (known after apply)
    + id        = (known after apply)
    + name      = "eks-devsecops-allow-eks-asg"
    + path      = "/"
    + policy    = jsonencode(
      {
      + Statement = [
      + {
      + Action   = [
      + "autoscaling:DescribeAutoScalingGroups",
      + "autoscaling:DescribeAutoScalingInstances",
      + "autoscaling:DescribeLaunchConfigurations",
      + "autoscaling:SetDesiredCapacity",
      + "autoscaling:TerminateInstanceInAutoScalingGroup",
      + "autoscaling:DescribeTags",
      + "ec2:DescribeLaunchTemplateVersions",
      + "eks:*",
      ]
      + Effect   = "Allow"
      + Resource = "*"
      },
      ]
      + Version   = "2012-10-17"
      }
      )
    + policy_id = (known after apply)
    + tags_all  = (known after apply)
      }

# aws_iam_role.codepipeline_role will be created
+ resource "aws_iam_role" "codepipeline_role" {
    + arn                   = (known after apply)
    + assume_role_policy    = jsonencode(
      {
      + Statement = [
      + {
      + Action    = "sts:AssumeRole"
      + Effect    = "Allow"
      + Principal = {
      + Service = "codepipeline.amazonaws.com"
      }
      },
      ]
      + Version   = "2012-10-17"
      }
      )
    + create_date           = (known after apply)
    + force_detach_policies = false
    + id                    = (known after apply)
    + managed_policy_arns   = (known after apply)
    + max_session_duration  = 3600
    + name                  = "houston-test-role"
    + path                  = "/"
    + tags_all              = (known after apply)
    + unique_id             = (known after apply)

    + inline_policy {
        + name   = (known after apply)
        + policy = (known after apply)
          }
          }

# aws_iam_role.devsecops-austin-codebuild will be created
+ resource "aws_iam_role" "devsecops-austin-codebuild" {
    + arn                   = (known after apply)
    + assume_role_policy    = jsonencode(
      {
      + Statement = [
      + {
      + Action    = "sts:AssumeRole"
      + Effect    = "Allow"
      + Principal = {
      + Service = "codebuild.amazonaws.com"
      }
      },
      ]
      + Version   = "2012-10-17"
      }
      )
    + create_date           = (known after apply)
    + force_detach_policies = false
    + id                    = (known after apply)
    + managed_policy_arns   = (known after apply)
    + max_session_duration  = 3600
    + name                  = "devsecops-houston-codebuild"
    + path                  = "/"
    + tags_all              = (known after apply)
    + unique_id             = (known after apply)

    + inline_policy {
        + name   = (known after apply)
        + policy = (known after apply)
          }
          }

# aws_iam_role.main will be created
+ resource "aws_iam_role" "main" {
    + arn                   = (known after apply)
    + assume_role_policy    = jsonencode(
      {
      + Statement = [
      + {
      + Action    = "sts:AssumeRole"
      + Effect    = "Allow"
      + Principal = {
      + Service = "eks.amazonaws.com"
      }
      },
      ]
      + Version   = "2012-10-17"
      }
      )
    + create_date           = (known after apply)
    + description           = "Role for eks service"
    + force_detach_policies = false
    + id                    = (known after apply)
    + managed_policy_arns   = (known after apply)
    + max_session_duration  = 3600
    + name                  = "eks-devsecops"
    + path                  = "/"
    + tags_all              = (known after apply)
    + unique_id             = (known after apply)

    + inline_policy {
        + name   = (known after apply)
        + policy = (known after apply)
          }
          }

# aws_iam_role_policy.codepipeline_policy will be created
+ resource "aws_iam_role_policy" "codepipeline_policy" {
    + id     = (known after apply)
    + name   = "houston-codepipeline_policy"
    + policy = (known after apply)
    + role   = (known after apply)
      }

# aws_iam_role_policy.devsecops-austin-codebuild will be created
+ resource "aws_iam_role_policy" "devsecops-austin-codebuild" {
    + id     = (known after apply)
    + name   = (known after apply)
    + policy = (known after apply)
    + role   = "devsecops-houston-codebuild"
      }

# aws_iam_role_policy_attachment.AmazonEKSClusterPolicy will be created
+ resource "aws_iam_role_policy_attachment" "AmazonEKSClusterPolicy" {
    + id         = (known after apply)
    + policy_arn = "arn:aws:iam::aws:policy/AmazonEKSClusterPolicy"
    + role       = "devsecops-houston-codebuild"
      }

# aws_iam_role_policy_attachment.AmazonEKSServicePolicy will be created
+ resource "aws_iam_role_policy_attachment" "AmazonEKSServicePolicy" {
    + id         = (known after apply)
    + policy_arn = "arn:aws:iam::aws:policy/AmazonEKSServicePolicy"
    + role       = "devsecops-houston-codebuild"
      }

# aws_s3_bucket.codebuild_s3 will be created
+ resource "aws_s3_bucket" "codebuild_s3" {
    + acceleration_status         = (known after apply)
    + acl                         = "private"
    + arn                         = (known after apply)
    + bucket                      = "devsecops-houston-codebuild"
    + bucket_domain_name          = (known after apply)
    + bucket_regional_domain_name = (known after apply)
    + force_destroy               = false
    + hosted_zone_id              = (known after apply)
    + id                          = (known after apply)
    + region                      = (known after apply)
    + request_payer               = (known after apply)
    + tags_all                    = (known after apply)
    + website_domain              = (known after apply)
    + website_endpoint            = (known after apply)

    + versioning {
        + enabled    = (known after apply)
        + mfa_delete = (known after apply)
          }
          }

# aws_s3_bucket.codepipeline_bucket will be created
+ resource "aws_s3_bucket" "codepipeline_bucket" {
    + acceleration_status         = (known after apply)
    + acl                         = "private"
    + arn                         = (known after apply)
    + bucket                      = "houston-devsecops-code"
    + bucket_domain_name          = (known after apply)
    + bucket_regional_domain_name = (known after apply)
    + force_destroy               = false
    + hosted_zone_id              = (known after apply)
    + id                          = (known after apply)
    + region                      = (known after apply)
    + request_payer               = (known after apply)
    + tags_all                    = (known after apply)
    + website_domain              = (known after apply)
    + website_endpoint            = (known after apply)

    + versioning {
        + enabled    = (known after apply)
        + mfa_delete = (known after apply)
          }
          }

# aws_ssm_parameter.account_id will be created
+ resource "aws_ssm_parameter" "account_id" {
    + arn       = (known after apply)
    + data_type = (known after apply)
    + id        = (known after apply)
    + key_id    = (known after apply)
    + name      = "ACCOUNT_ID"
    + overwrite = true
    + tags_all  = (known after apply)
    + tier      = "Standard"
    + type      = "String"
    + value     = (sensitive value)
    + version   = (known after apply)
      }

Plan: 19 to add, 0 to change, 0 to destroy.
aws_cloudwatch_log_group.codebuild: Creating...
aws_iam_role.codepipeline_role: Creating...
aws_ssm_parameter.account_id: Creating...
aws_iam_role.devsecops-austin-codebuild: Creating...
aws_ecr_repository.golang_example: Creating...
aws_iam_role.main: Creating...
aws_s3_bucket.codepipeline_bucket: Creating...
aws_codecommit_repository.devsecops: Creating...
aws_iam_policy.allow-eks-asg: Creating...
aws_s3_bucket.codebuild_s3: Creating...
aws_cloudwatch_log_group.codebuild: Creation complete after 0s [id=devsecops-houston-codebuild]
aws_cloudwatch_log_stream.codebuild: Creating...
aws_ecr_repository.golang_example: Creation complete after 0s [id=golang_example-houston]
aws_cloudwatch_log_stream.codebuild: Creation complete after 0s [id=devsecops-houston-codebuild]
aws_ssm_parameter.account_id: Creation complete after 0s [id=ACCOUNT_ID]
aws_codecommit_repository.devsecops: Creation complete after 0s [id=houston-devsecops-repo]
aws_iam_policy.allow-eks-asg: Creation complete after 1s [id=arn:aws:iam::123456789012:policy/eks-devsecops-allow-eks-asg]
aws_iam_role.devsecops-austin-codebuild: Creation complete after 1s [id=devsecops-houston-codebuild]
aws_iam_role_policy_attachment.AmazonEKSServicePolicy: Creating...
aws_iam_role_policy_attachment.AmazonEKSClusterPolicy: Creating...
aws_iam_role.main: Creation complete after 1s [id=eks-devsecops]
aws_s3_bucket.codebuild_s3: Creation complete after 1s [id=devsecops-houston-codebuild]
aws_iam_role.codepipeline_role: Creation complete after 1s [id=houston-test-role]
aws_codebuild_project.devsecops-austin-codebuild-DEPLOY: Creating...
aws_codebuild_project.devsecops-austin-codebuild-BUILD: Creating...
aws_codebuild_project.devsecops-austin-codebuild-STATIC: Creating...
aws_s3_bucket.codepipeline_bucket: Creation complete after 1s [id=houston-devsecops-code]
aws_iam_role_policy.devsecops-austin-codebuild: Creating...
aws_iam_role_policy.codepipeline_policy: Creating...
aws_codepipeline.codepipeline: Creating...
aws_iam_role_policy_attachment.AmazonEKSServicePolicy: Creation complete after 1s [id=devsecops-houston-codebuild-20210911231810424800000002]
aws_iam_role_policy_attachment.AmazonEKSClusterPolicy: Creation complete after 1s [id=devsecops-houston-codebuild-20210911231810453900000003]
aws_iam_role_policy.codepipeline_policy: Creation complete after 1s [id=houston-test-role:houston-codepipeline_policy]
aws_iam_role_policy.devsecops-austin-codebuild: Creation complete after 1s [id=devsecops-houston-codebuild:terraform-20210911231810411800000001]
aws_codepipeline.codepipeline: Creation complete after 9s [id=houston-devsecops-pipeline]
aws_codebuild_project.devsecops-austin-codebuild-DEPLOY: Still creating... [10s elapsed]
aws_codebuild_project.devsecops-austin-codebuild-STATIC: Still creating... [10s elapsed]
aws_codebuild_project.devsecops-austin-codebuild-BUILD: Still creating... [10s elapsed]
aws_codebuild_project.devsecops-austin-codebuild-BUILD: Creation complete after 16s [id=arn:aws:codebuild:us-west-2:123456789012:project/devsecops-houston-codebuild-BUILD]
aws_codebuild_project.devsecops-austin-codebuild-STATIC: Creation complete after 16s [id=arn:aws:codebuild:us-west-2:123456789012:project/devsecops-houston-codebuild-STATIC]
aws_codebuild_project.devsecops-austin-codebuild-DEPLOY: Creation complete after 16s [id=arn:aws:codebuild:us-west-2:123456789012:project/devsecops-houston-codebuild-DEPLOY]

Apply complete! Resources: 19 added, 0 changed, 0 destroyed.
```
{{% /expand%}}

Navigate to AWS codepipeline and ensure your Pipeline created properly 

https://us-west-2.console.aws.amazon.com/codesuite/codepipeline/pipeline

Don't worry about the failure, were going to fix that in the next Exercise
