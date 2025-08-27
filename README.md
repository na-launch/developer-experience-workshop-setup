# Developer Experience Workshop Setup

This repository contains the setup instructions to build the Developer Experience Workshop.  Refer
to this link for [workshop module content](https://github.com/na-launch/developer-experience-workshop).

## Prerequisites

For this workshop, we'll assume that an OpenShift 4 cluster has already been installed.  These are
the following operators that will be installed, if not present already:

Workshop infrastructure:
- Red Hat Single Sign-On (Keycloak)
- Vault
- External Secrets
- Red Hat OpenShift GitOps (ArgoCD)
- GitLab (self-hosted)
- Red Hat Developer Hub

Workshop module content:
- Red Hat OpenShift Dev Spaces
- Streams for Apache Kafka
- Red Hat OpenShift Serverless
- Red Hat build of OpenTelemetry
- Minio (for S3 storage for OpenTelemetry)

## Configure and Install

1. Clone this Git repository: https://github.com/poc-examples/cluster-bootstrapper

2. Replace [config.yaml](https://github.com/poc-examples/cluster-bootstrapper/blob/main/config.yaml)
in the cloned repository with this example [config.yaml](examples/config.yaml).

The domain is name of your cluster domain (i.e. your OpenShift API endpoint is https://api.<cluster-domain>)
```
      domain: set-me
```

Comment this section out as we won't need to provision a new cluster:
```
#     compute:
#       workers: 6
#     providerCredentials:
#       aws_access_key_id: ""
#       aws_secret_access_key: ""
#       region: "us-east-2"
#       rosa_token: ""
```

Update the version of the chart as needed.  The latest published version can be found in [Chart.yaml](https://github.com/poc-examples/charts/blob/main/charts/orchestrator/Chart.yaml)
```
      workshop:
        chart:
          enabled: true
          name: orchestrator
          version: 0.0.80
```

Run `make configure` to install the workshop infrastructure components and configure Developer Hub
plugins to access those components (i.e. GitLab, OpenShift GitOps, ...).  The setup files can be
(found here)[https://github.com/poc-examples/charts/tree/main/charts/developer_hub/templates) and
toggling on/off individual operators can be (found here)[https://github.com/poc-examples/ansible-collections/blob/c5dddfd53af223604856f79811a0d504dc8e404f/bootstrap/workshop/roles/gitops-instance/tasks/main.yaml#L493).

## Deploy Workshop Template

Now that the infrastructure components have been installed, log into Developer Hub as an administrator.
Import the workshop template in Developer Hub by navigating to Catalog -> Self-service -> Register
Existing Component and using [this template](https://github.com/poc-examples/software-templates/blob/main/workshop/developer-experience/template.yaml)

Using the newly imported template will deploy the workshop by writing files to the internal GitLab
where OpenShift GitOps will sync to the repository and build out the operators required.  The
template will also ask the user whether extra capacity for Dev Spaces and Kafka need to be provisioned.
Kicking off off the template writes towards the self-hosted GitLab, and OpenShift GitOps will sync
changes and build out the operators required for end users to use the workshop module content.

The template obsoletes the following [alternative instructions](https://github.com/na-launch/developer-experience-workshop-docs)
to build and host workshop content.

## Updating Workshop Content

The workshop content is built in mkdocs format.  To update the website, fork this Git repository,
update the [docs](https://github.com/poc-examples/software-templates/tree/main/workshop/developer-experience/skeleton/repository/docs)m
and upload the new software template.

