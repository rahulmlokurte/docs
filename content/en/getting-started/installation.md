---
title: "Installation"
linkTitle: "Installation"
weight: 3
description: >
  Basic installation guide to get started with LocalStack on your local machine.
cascade:
  type: docs
---

This installation guide provides the basic instructions to install LocalStack for the first time, and assumes you are already familiar with the Docker and Python packaging ecosystems.

## How to install LocalStack

The first thing when getting started with LocalStack is to choose your preferred way of starting and managing your LocalStack instance.

LocalStack currently provides the following options:

- [LocalStack CLI]({{< ref "#localstack-cli" >}})\
  The easiest way to start and manage LocalStack - either on your machine, in a Docker container on your machine, or even on a remote Docker host.

- [LocalStack Cockpit]({{< ref "#localstack-cockpit" >}})\
  Get a desktop experience and work with your local LocalStack instance via the UI.

- [Docker]({{< ref "#docker" >}})\
  Use the `docker` CLI to manually start the LocalStack Docker container.

- [Docker-Compose]({{< ref "#docker-compose" >}})\
  Use `docker-compose` to configure and start your LocalStack Docker container.

- [Helm]({{< ref "#helm" >}})\
  Use `helm` to create a LocalStack deployment in a Kubernetes cluster.

LocalStack runs inside a Docker container, and the above options are different ways to start and manage the LocalStack Docker container. The LocalStack CLI is the easiest way to get started, and we recommend using it for your first steps with LocalStack. For a comprehensive overview of the LocalStack images, check out our [Docker images documentation]({{< ref "docker-images" >}}).

### LocalStack CLI

The LocalStack CLI aims to simplify starting and managing LocalStack. It provides convenience features to start LocalStack on your local machine, as a Docker container on your machine, or even on a remote Docker host. In addition you can easily check the status or open a shell in your LocalStack instance if you want to take a deep-dive.

#### Prerequisites

Please make sure to install the following tools on your machine before moving ahead:

- [`python`](https://docs.python.org/3/using/index.html) (Python 3.7 up to 3.10 is supported)
- [`pip`](https://pip.pypa.io/en/stable/installation/) (Python package manager)
- [`docker`](https://docs.docker.com/get-docker/)

#### Installation

Download the latest LocalStack release:

{{< tabpane >}}
{{< tab header="macOS brew" lang="shell" >}}
brew install localstack
{{< /tab >}}
{{< tab header="macOS/Linux PyPI" lang="shell" >}}
python3 -m pip install localstack
{{< /tab >}}
{{< tab header="Windows" lang="shell" >}}
python -m pip install localstack
{{< /tab >}}
{{< /tabpane >}}

{{< alert title="Note" >}}
To download a specific version of LocalStack, check out our [release page](https://github.com/localstack/localstack) and download it in the following manner:

{{< command >}} 
$ python3 -m pip install localstack==<version>
{{< / command >}}

Here `<version>` depicts the particular LocalStack version that you would like to download and use.
{{< /alert >}}

{{< alert title="Important" color="danger" >}}
Do not use `sudo` or the `root` user - LocalStack should be installed and started entirely under a local non-root user. 
If you have problems with permissions in MacOS X Sierra, install with `python3 -m pip install --user localstack`.
{{< /alert >}}

Afterwards you should be able to use the LocalStack CLI in your terminal:

{{< command >}}
$ localstack --help
Usage: localstack [OPTIONS] COMMAND [ARGS]...

  The LocalStack Command Line Interface (CLI)

Options:
...
{{< / command >}}

#### Updates

The LocalStack CLI also allows you to easily update the different components of LocalStack. To check the various options available for updating, run:

{{< command >}}
$ localstack update --help

Usage: localstack update [OPTIONS] COMMAND [ARGS]...

  Update LocalStack components

Options:
  --help  Show this message and exit.

Commands:
  all             Update all LocalStack components
  docker-images   Update container images LocalStack depends on
  localstack-cli  Update LocalStack CLI tools
{{< / command >}}

You can decide to update the CLI itself, the LocalStack Docker images, or all at once:

{{< command >}}
$ # Update all components
$ localstack update all
$ # Only update the LocalStack docker images
$ localstack update docker-images
$ # Only update the LocalStack CLI
$ localstack update localstack-cli
{{< / command >}}

#### Starting LocalStack with the LocalStack CLI

By default, LocalStack is started inside a Docker container by running:

{{< command >}}
$ localstack start
{{< / command >}}

{{< alert title="Notes" >}}
- This command loads all services provided by LocalStack, they will however be started on the first request reaching this service.

- By default, LocalStack uses the image tagged `latest` that is cached on your machine, and will **not** pull the latest image automatically from Docker Hub (i.e., the image needs to be pulled manually if needed).

- The default image `localstack/localstack` in DockerHub refers to the community version of LocalStack. The `localstack/localstack-pro` image refers to the Pro version of LocalStack. Previously we maintained `localstack-light` and `localstack-full` images which has been deprecated and will be removed with the LocalStack 2.0 release.

- When an [API key]({{< ref "api-key" >}}) is set in your environment, `localstack start` will attempt to start LocalStack with the `localstack/localtack-pro` image.
  A successful key activation is required to start LocalStack Pro but can be disabled by setting `ACTIVATE_PRO=0`.
{{< /alert >}}

### LocalStack Cockpit

See [LocalStack Cockpit]({{< ref "cockpit" >}}).

### Docker

To use LocalStack without the [LocalStack CLI]({{< ref "#localstack-cli" >}}), you have the option of running the LocalStack Docker container by yourself. This method requires more manual steps and configuration, but it gives you more control over the container settings and environment variables.

#### Prerequisites

Please make sure that you have a working [`docker` environment](https://docs.docker.com/get-docker/) on your machine before moving on.
You can check if `docker` is correctly configured on your machine by executing `docker info` in your terminal. If it does not report an error (but shows information on your Docker system), you're good to go.

#### Starting LocalStack with Docker

You can start the Docker container simply by executing the following `docker run` command:

{{< tabpane >}}
{{< tab header="Community" lang="shell" >}}
docker run \
  --rm -it \
  -p 4566:4566 \
  -p 4510-4559:4510-4559 \
  localstack/localstack
{{< /tab >}}
{{< tab header="Pro" lang="shell" >}}
docker run \
  --rm -it \
  -p 4566:4566 \
  -p 4510-4559:4510-4559 \
  -e LOCALSTACK_API_KEY=${LOCALSTACK_API_KEY:- } \
  localstack/localstack-pro{{< /tab >}}
{{< /tabpane >}}

{{< alert title="Notes" >}}
- This command pulls the current nightly build from the `master` branch (if you don't have the image locally) and **not** the latest supported version. If you want to use a specific version of LocalStack, use the appropriate tag: `docker run --rm -it -p 4566:4566 -p 4510-4559:4510-4559 localstack/localstack:<tag>`. Check-out the [LocalStack releases](https://github.com/localstack/localstack/releases) to know more about specific LocalStack versions.

- If you are using LocalStack with an [API key]({{< ref "api-key" >}}), you need to specify the image tag as `localstack/localstack-pro` in your Docker setup. Going forward, `localstack/localstack-pro` image will contain our Pro-supported services and APIs.

- This command reuses the image if it's already on your machine, i.e. it will **not** pull the latest image automatically from Docker Hub.

- This command does not bind all ports that are potentially used by LocalStack, nor does it mount any volumes.
  When using Docker to manually start LocalStack, you will have to configure the container on your own (see [docker-compose-pro.yml](https://github.com/localstack/localstack/blob/master/docker-compose-pro.yml) and [Configuration]({{< ref "configuration" >}})).
  This could be seen as the "expert mode" of starting LocalStack.
  If you are looking for a simpler method of starting LocalStack, please use the [LocalStack CLI]({{< ref "#localstack-cli" >}}).

- To facilitate interoperability, configuration variables can be prefixed with `LOCALSTACK_` in docker. For instance, setting `LOCALSTACK_PERSISTENCE=1` is equivalent to `PERSISTENCE=1`.

- To configure an API key, refer to the [API Key](https://docs.localstack.cloud/getting-started/api-key/) documentation.
{{< /alert >}}

### Docker-Compose

If you want to manually manage your Docker container, it's usually a good idea to use [`docker-compose`](https://docs.docker.com/compose/reference/) in order to simplify your container configuration.

#### Prerequisites

- [`docker`](https://docs.docker.com/get-docker/)
- [`docker-compose`](https://docs.docker.com/compose/install/) (version 1.9.0+)

#### Starting LocalStack with Docker-Compose

You can start LocalStack with [Docker Compose](https://docs.docker.com/compose/) by configuring a `docker-compose.yml` file. Currently, `docker-compose` version 1.9.0+ is supported.

{{< tabpane >}}
{{< tab header="Community" lang="yml" >}}
version: "3.8"

services:
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME-localstack_main}"
    image: localstack/localstack
    ports:
      - "127.0.0.1:4566:4566"            # LocalStack Gateway
      - "127.0.0.1:4510-4559:4510-4559"  # external services port range
    environment:
      - DEBUG=${DEBUG-}
      - DOCKER_HOST=unix:///var/run/docker.sock
    volumes:
      - "${LOCALSTACK_VOLUME_DIR:-./volume}:/var/lib/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
{{< /tab >}}
{{< tab header="Pro" lang="yml" >}}
version: "3.8"

services:
  localstack:
    container_name: "${LOCALSTACK_DOCKER_NAME-localstack_main}"
    image: localstack/localstack-pro  # required for Pro
    ports:
      - "127.0.0.1:4566:4566"            # LocalStack Gateway
      - "127.0.0.1:4510-4559:4510-4559"  # external services port range
      - "127.0.0.1:53:53"                # DNS config (required for Pro)
      - "127.0.0.1:53:53/udp"            # DNS config (required for Pro)
      - "127.0.0.1:443:443"              # LocalStack HTTPS Gateway (required for Pro)
    environment:
      - DEBUG=${DEBUG-}
      - PERSISTENCE=${PERSISTENCE-}
      - LOCALSTACK_API_KEY=${LOCALSTACK_API_KEY-}  # required for Pro
      - DOCKER_HOST=unix:///var/run/docker.sock
    volumes:
      - "${LOCALSTACK_VOLUME_DIR:-./volume}:/var/lib/localstack"
      - "/var/run/docker.sock:/var/run/docker.sock"
{{< /tab >}}
{{< /tabpane >}}

Start the container by running the following command:

{{< command >}}
$ docker-compose up
{{< / command >}}

{{< alert title="Notes" >}}
- This command pulls the current nightly build from the `master` branch (if you don't have the image locally) and **not** the latest supported version. If you want to use a specific version, set the appropriate localstack image tag at `services.localstack.image` in the `docker-compose.yml` file (for example `localstack/localstack:<version>`).

- If you are using LocalStack with an [API key]({{< ref "api-key" >}}), you need to specify the image tag as `localstack/localstack-pro` in the `docker-compose.yml` file. Going forward, `localstack/localstack-pro` image will contain our Pro-supported services and APIs.

- This command reuses the image if it's already on your machine, i.e. it will **not** pull the latest image automatically from Docker Hub.

- Mounting the Docker socket `/var/run/docker.sock` as a volume is required for the Lambda service. Check out the [Lambda providers]({{< ref "user-guide/aws/lambda" >}}) documentation for more information.

- To facilitate interoperability, configuration variables can be prefixed with `LOCALSTACK_` in docker. For instance, setting `LOCALSTACK_PERSISTENCE=1` is equivalent to `PERSISTENCE=1`.

- If using the Docker default bridge network using `network_mode: bridge`, container name resolution will not work inside your containers. Please consider removing it, if this functionality is needed.

- To configure an API key, refer to the [API Key](https://docs.localstack.cloud/getting-started/api-key/) documentation.
{{< /alert >}}

Please note that there are a few pitfalls when configuring your stack manually via docker-compose (e.g., required container name, Docker network, volume mounts, and environment variables). We recommend using the LocalStack CLI to validate your configuration, which will print warning messages in case it detects any potential misconfigurations:

{{< command >}}
$ localstack config validate
...
{{< / command >}}

### Helm

If you want to deploy LocalStack in your [Kubernetes](https://kubernetes.io) cluster, you can use [Helm](https://helm.sh).

#### Prerequisites

- [Kubernetes](https://kubernetes.io)
- [Helm](https://helm.sh/docs/intro/install/)

#### Deploy LocalStack using Helm

You can deploy LocalStack in a Kubernetes cluster by running these commands:
{{< command >}}
$ helm repo add localstack-repo https://helm.localstack.cloud
$ helm upgrade --install localstack localstack-repo/localstack
{{< / command >}}

The Helm charts are not maintained in the main repository, but in a [separate one](https://github.com/localstack/helm-charts).

### Troubleshooting

- The LocalStack CLI installation is successful, but I cannot execute `localstack` on my terminal.

  If you can successfully install LocalStack using `pip` but you cannot use it in your terminal, you most likely haven't set up your operating system's / terminal's `PATH` variable (in order to tell them where to find programs installed via `pip`).
  - If you are using Windows, you can enable the `PATH` configuration when installing Python, [as described in the official docs of Python](https://docs.python.org/3/using/windows.html#finding-the-python-executable).
  - If you are using a MacOS or Linux operating system, please make sure that the `PATH` is correctly set up - either system wide, or in your terminal.

  As a workaround you can call the LocalStack CLI python module directly:
  {{< command >}}
  $ python3 -m localstack.cli.main
  {{< / command >}}

- How should I access the LocalStack logs on my local machine?

  You can now avail logging output and error reporting using LocalStack logs. To access the logs, run the following command:

  {{< command >}}
  $ localstack logs 
  {{< / command >}}
  
  AWS requests are now logged uniformly in the INFO log level (set by default or when `DEBUG=0`). The shape is `AWS <service>.<operation> => <http-status> (<error type>)`. Requests to HTTP endpoints are logged in a similar way:
  
  ```sh
  2022-09-12T10:39:21.165  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.ListBuckets => 200
  2022-09-12T10:39:41.315  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.CreateBucket => 200
  2022-09-12T10:40:04.662  INFO --- [   asgi_gw_0] localstack.request.aws     : AWS s3.PutObject => 200
  2022-09-12T11:01:55.799  INFO --- [   asgi_gw_0] localstack.request.http    : GET / => 200
  ```

- How should I share the LocalStack logs to discover issues?

  You can now share the LocalStack logs with us to help us discover issues. To share the logs, run our diagnostic endpoint:

  {{< command >}}
  $ curl -s localhost:4566/_localstack/diagnose | gzip -cf > diagnose.json.gz
  {{< / command >}}

  Ensure that the diagnostic endpoint is run after you have tried reproducing the affected task. After running the task, run the diagnostic endpoint and share the archive file with your team members or LocalStack Support.

## What's next?

Now that you have LocalStack up and running, the following resources might be useful for your next steps:
- Check out our [Quickstart guide]({{< ref "quickstart" >}}) if you are a new user to get started with LocalStack quickly.
- [Use the LocalStack integrations]({{< ref "integrations" >}}) to interact with LocalStack and other integrated tools, for example:
  - Use `awslocal` to use the AWS CLI against your local cloud!
  - Use the Serverless Framework with LocalStack!
  - And many more!
- [Find out how to configure LocalStack]({{< ref "configuration" >}}) such that it perfectly fits your need.
- [Use LocalStack in your CI environment]({{< ref "user-guide/ci" >}}) to increase your code quality.
- [Checkout LocalStack's Cloud Developer Tools]({{< ref "user-guide/tools" >}}) to further increase your development efficiency with LocalStack.
- Find out about the ways you can [configure LocalStack]({{< ref "configuration" >}}).
