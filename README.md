# Example Environment for AWS

This repo contains manifests to manage control planes in an Upbound Environment
deployment. It utilizes IRSA to authenticate to AWS.

It assumes that your EKS cluster is prepared by following the instructions [here](https://github.com/upbound-demo/environments-instructions/).

## Get Started

### Preparing Your Own Repository

* Fork the repo and clone your fork.
  * This is needed so that you can push details specific to your clusters and
    have FluxCD pick them up from that repo.
* Replace the following strings with values specific to your cluster:
  * `oidc.eks.us-east-1.amazonaws.com/id/C09D53376BDFD92A00BD5042AE19AEFF`: Run
    the following command and replace the output with all instances.
    ```bash
    # CLUSTER_NAME should be the name of your EKS cluster.
    # REGION should be the region your EKS cluster resides in.
    aws eks describe-cluster --name ${CLUSTER_NAME} --region ${REGION} --query "cluster.identity.oidc.issuer" --output text | sed -e "s/^https:\/\///"
    ```
  * `609897127049`: You need to replace all instances of this number with your
    AWS account ID which you can get with the following command:
    ```bash
    aws sts get-caller-identity --query "Account" --output text
    ```
* Create a new commit to push changes.

### Bootstrapping Flux

* Create a [Github Personal Access Token](https://github.com/settings/tokens) with access to your fork of this
  repository. If it's public, `public_repo` under `repo` should be enough.
* Bootstrap Flux in your EKS cluster.
  ```bash
  # GITHUB_ORG is the Github organization your fork resides in.
  # REPO is the name of the repository of your fork.
  # GITHUB_PAT is the personal access token you just created.
  echo "${GITHUB_PAT}" | \
  flux bootstrap github \
    --owner=${GITHUB_ORG} \
    --repository=${REPO} \
    --branch=main \
    --path=./main \
    --token-auth
  ```
* Once it's finished, you can see the control planes.
  ```bash
  kubectl get controlplane
  ```
  You can see the `Role`s that are created for each control plane as well.
  ```bash
  kubectl get role.iam
  ```

Enjoy!