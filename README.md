## Overview

The `kubectl-jump-box` is a specialized temporary Kubernetes pod designed to assist developers and system administrators with debugging and network analysis within the confines of a Kubernetes cluster. The jump-box provides an interactive shell environment, pre-equipped with a suite of essential tools. These tools facilitate a range of diagnostics and troubleshooting tasks, offering deeper insights than what is typically available from outside the cluster environment.

## Features

- **Pre-configured Tools**: Comes with commonly used CLI tools such as `awscli-v2`, `kubectl`, `dnsutils`, `jq`, `tmux`, `psql`, and `pg_dump`.
- **Multi-Arch Support**: The `kubectl-jump-box` is built to support multiple architectures, ensuring compatibility across various environments. `buildx` command - 
  - ```shell
    docker buildx build . -t ghcr.io/milapj/kubectl-jump-box:0.0.13 --platform linux/amd64,linux/arm64/v8,linux/arm/v7  --provenance=false --push
    ```
- **Immediate Access**: Easily accessible within the Kubernetes cluster, providing an on-demand debugging environment.

## Installation

1. To deploy the jump-box in your Kubernetes cluster, run the following command:

    ```shell
    kubectl run -i --tty jump-box --image=ghcr.io/milapj/kubectl-jump-box:0.0.13 -n my-app
    ```

   This will create an interactive pod named `jump-box` within the `my-app` namespace.

   ![jump-box draw io drawio](https://github.com/milapj/kubectl-jump-box/assets/9828402/e06e6c9c-1e6d-491d-8694-c0acf2f49695)

## Usage Scenarios

Some of the scenarios in which `kubectl-jump-box` can be useful :

- **DNS Troubleshooting**: Confirm the functionality of Kubernetes DNS resolution within the cluster.

  - Service discovery is a fundamental element within the Kubernetes architecture, enabling the proper distribution of incoming traffic to the appropriate workloads within the cluster. DNS is instrumental in facilitating this mechanism. Gaining a comprehension of Kubernetes' DNS and service discovery mechanisms is beneficial for troubleshooting potential problems.

    ```shell
    kubectl run -i --tty jump-box --image=ghcr.io/milapj/kubectl-jump-box:0.0.13 -n my-app

    bash-5.2$ nslookup service1

    Server:		10.35.240.10
    Address:	10.35.240.10#53

    Name:	service.my-app.svc.cluster.local
    Address: 10.35.254.119
    ```
  - learn more about Kubernetes DNS Resolution [here](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)


- **Permission Verification**: Verify if a service account has the required permissions for specific cluster operations. For example, if your Pod has to connect to a particular S3 bucket or to a particular image registry -
    ```shell
    kubectl run -i --tty jump-box --image=ghcr.io/milapj/kubectl-jump-box:0.0.13 --serviceaccount='my-app-sa' -n my-app
    
    bash-5.2$ aws s3 ls get-object --bucket my-bucket
    ```
- **Application Testing**: Useful for in-depth application troubleshooting when `kubectl logs` does not provide sufficient information. 

- **Database Administration**: Run DDL and DML commands against a postgresql database using `psql` client. Backup the database using `pg_dump` utility.

