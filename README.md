# Creating a docker from scratch

What is a container? According to [Docker's definition](https://www.docker.com/resources/what-container/); 

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

In essense, a fabricated image ([see example](https://hub.docker.com/r/rawworks/rawworks-cicd-toolset)) CONTAINs various layers ([see example](https://hub.docker.com/layers/rawworks/rawworks-cicd-toolset/0.0.1/images/sha256-244c6ee06fe14150a712dc37bfb99c00c752e4118a98fd9f3c79720a0100cdaf)) to host a, preferably cloud-native, application.

Docker supports a wide variaty of processor architectures (x86-64/ARM/ARM64/PPC64/pc64le/s390x/riscv64/mips64le) and operating systems (GNU+Linux/Windows.FreeBSD and to a certain extend macOS).

The challenge is; creating the container image having the smallest possible footprint while ensuring all needed functionality to run that specific application.

## Preparation for Docker / Docker Desktop on GNU/Linux (Debian)

<details close>
<summary>Installing Docker Desktop</summary>

### Set up the apt repository

    # Add Docker's official GPG key:
    sudo apt update
    sudo apt install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    # Updating the repo cache
    sudo apt update

### Install the Docker Desktop

    url='https://desktop.docker.com/linux/main/amd64/149282/docker-desktop-4.30.0-amd64.deb'
    file=`basename "$url"`
    wget "$url" -O "$file"
    sudo apt install ./$file -y

### Optional: Install the latest Docker packages

    sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
</details>



<details close>
<summary>Verify the Docker service and socket daemons</summary>

### Check status of the Docker API socket daemon

    systemctl status docker.socket

### Check status of the Docker Service daemon

    systemctl status docker.service 
</details>



<details close>
<summary>Running Docker Desktop</summary>

### Install the Docker Desktop daemon

    systemctl --user enable docker-desktop

### Starting the Docker Desktop

    systemctl --user start docker-desktop

### Stopping the Docker Desktop

    systemctl --user stop docker-desktop
</details>




## Sanity check on the Docker Engine

<details close>
<summary>Kickstarting a canonical container</summary>

### Running the ```hello-world``` docker

    sudo docker run hello-world

### The output

While running this command, the docker engine will check whether the needed image is already stored in the local cache and if needed download it subsequently.

Next up is the ```Hello from Docker!``` sentence from the [hello-world container](https://github.com/docker-library/hello-world) which confirms the sanity

    Unable to find image 'hello-world:latest' locally
    latest: Pulling from library/hello-world
    c1ec31eb5944: Pull complete 
    Digest: sha256:266b191e926f65542fa8daaec01a192c4d292bff79426f47300a046e1bc576fd
    Status: Downloaded newer image for hello-world:latest

    Hello from Docker!
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (amd64)
    3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker ID:
    https://hub.docker.com/

    For more examples and ideas, visit:
    https://docs.docker.com/get-started/
</details>

## The dockerfile commands

A dockerfile is a text-based file used as the source of a container. The `dockerfile` contains all the specifications for the main application to run.

Which instructions can be used in the dockerfile? Let's see what is used in the example docker from [hub.docker.com](https://hub.docker.com/layers/rawworks/rawworks-cicd-toolset/0.0.1/images/sha256-244c6ee06fe14150a712dc37bfb99c00c752e4118a98fd9f3c79720a0100cdaf?context=explore)

<details close>
<summary><code>FROM ubuntu:latest</code></summary><br>
This specifies the base image as fundament of the container.The base image can either be a vanilla OS image or and already defined OS + application image.<br><br>
<a href="https://docs.docker.com/reference/dockerfile/#from" target="_blank">Documentation and example code here</a> <br><br>
Selecting a base image for a Docker container can vary depending on the specific requirements of the application and can occasionally be challenging.<br><br> Examples of OS base images from <a href src="https://hub.docker.com/search?sort=updated_at&order=desc&categories=Operating%20Systems&image_filter=official" target="_blank">Dockers' Official Images</a>:
<ul>
<li>GNU/Linux</li>
<ul>
<li><a href src="https://hub.docker.com/_/debian" target="_blank">debian</a> - Debian is a Linux distribution that's composed entirely of free and open-source software.</li>
<li><a href src="https://hub.docker.com/_/ubuntu" target="_blank">ubuntu</a> - Ubuntu is a Debian-based Linux operating system based on free software.</li>
<li><a href src="https://hub.docker.com/_/fedora" target="_blank">fedora</a> - Official Docker builds of Fedora</li>
<li><a href src="https://hub.docker.com/_/alpine" target="_blank">alpine</a> - A minimal Docker image based on Alpine Linux with a complete package index and only 5 MB in size!</li>
<li><a href src="https://hub.docker.com/_/archlinux" target="_blank">archlinux</a> - Arch Linux is a simple, lightweight Linux distribution aimed for flexibility.</li>
<li><a href src="https://hub.docker.com/_/photon" target="_blank">proton</a> - Photon OS is an open source minimal Linux container host.</li>
<li><a href src="https://hub.docker.com/_/busybox" target="_blank">busybox</a> - Busybox base image, The Swiss Army Knife of Embedded Linux</li>
<li><a href src="https://hub.docker.com/_/oraclelinux" target="_blank">oracle</a> - Official Docker builds of Oracle Linux</li>
<li><a href src="https://hub.docker.com/_/amazonlinux" target="_blank">amazonlinux</a> - Amazon Linux provides a stable, secure, and high-performance execution environment for applications</li>  
</ul>
<li>Microsoft Windows - <a href src="https://i.imgur.com/PmEhX7S.jpeg" target="_blankl">Note: this is closed source!</a></li>
<ul>
<li><a href src="https://hub.docker.com/_/microsoft-windows-nanoserver" _target="_blank">windows/nanoserver</a> - Nano Server</li>
<li><a href src="https://hub.docker.com/_/microsoft-windows-servercore" _target="_blank">windows/servercore</a> - Core</li>
<li><a href src="https://hub.docker.com/_/microsoft-windows-server" _target="_blank">windows/server</a> - Server</li>
<li><a href src="https://hub.docker.com/_/microsoft-windows" _target="_blank">Windows</a> - Windows</li>
</ul>    
</ul>
</details><br>

<details close>
<summary><code>ENV DEBIAN_FRONTEND=noninteractive</code></summary><br>
The <code>ENV</code> instruction in Docker sets an environment variable for all subsequent build steps. It can handle quotes and backslashes as you expect from other IaC-tools, making life easier for handling values with spaces or special characters.<br><br>
<a href="https://docs.docker.com/reference/dockerfile/#env" target="_blank">Documentation and example code here</a></details><br>

<details close>
<summary><code>RUN</code></summary><br>
The <code>RUN</code> instruction will execute any commands to create a new layer on top of the current image. The added layer is used and available as from this step in the Dockerfile. It can be used in two different forms:<br><br>
<ul>
<li>Shell Form:</li>
<ul>
<li>Use when you need shell features like variable expansion, command chaining, or piping.<br>
Ideal for complex commands that need to be executed together.<br>
Example: <code>RUN apt-get update && apt-get install -y python3</code></li>
</ul>
<li>Exec Form:</li>
<ul>
<li>Use for simple, straightforward commands that do not require shell features.<br>
Prefer for better security and avoiding shell injection risks.<br>
Example: <code>RUN ["apt-get", "install", "-y", "python3"]</code><br><br>
</ul>
</ul>
<a href="https://docs.docker.com/reference/dockerfile/#run" target="_blank">Documentation and example code here</a></details><br>

<details close>
<summary><code>ARG</code></summary><br>
The ARG instruction defines a variable that users can pass at build-time using the docker build command with the <code>--build-arg <varname>=<value></code> parameter flag.<br><br>
<strong>Warning: Do not use build arguments for passing secrets (e.g., user credentials, API tokens).<br>
Build arguments are visible in the docker history command and in provenance attestations, which are attached to the image by default if using Buildx GitHub Actions with a public GitHub repository.</strong>
Refer to the documentation, <code>RUN --mount=type=secret</code> section, for secure ways to use secrets when building images.
<a href="https://docs.docker.com/reference/dockerfile/#arg" target="_blank">Documentation and example code here</a></details><br>

<details close>
<summary><code>WORKDIR</code></summary><br>
The <code>WORKDIR</code> instruction sets the working directory for <code>RUN</code>, <code>CMD</code>, <code>ENTRYPOINT</code>, <code>COPY</code>, and <code>ADD</code> instructions that follow it. If the directory doesn't exist, it will be created.<br><br>
Multiple <code>WORKDIR</code> instructions can be used, and relative paths are based on the previous <code>WORKDIR</code>.<br><br>
<a href="https://docs.docker.com/reference/dockerfile/#workdir" target="_blank">Documentation and example code here</a></details><br>

<details close>
<summary><code>COPY</code></summary><br>
The <code>COPY</code> instruction copies new files or directories from a source path and adds them to the filesystem of the container at the destination path.<br><br>
<a href="https://docs.docker.com/reference/dockerfile/#copy" target="_blank">Documentation and example code here</a></details><br>

<details close>
<summary><code>ENTRYPOINT</code></summary><br>
The <code>ENTRYPOINT</code> instruction specifies the command that runs when a container starts, allowing the container to behave like an executable.<br><br>
Just like the <code>RUN</code> command the <code>ENTRYPOINT</code> can be used in two different forms:<br><br>
<ul>
<li>Shell Form</li>
<ul>
<li>Syntax: <code>ENTRYPOINT [command]</code></li>
<li>Example: <code>ENTRYPOINT /usr/bin/myapp</code></li>
<li>Use When: Shell features are needed.</li>
</ul>
<li>Exec Form</li>
<ul>
<li>Syntax: <code>ENTRYPOINT ["executable", "param1", "param2"]</code></li>
<li>Example: <code>ENTRYPOINT ["/usr/bin/myapp", "arg1"]</code></li>
<li>Use When: Preferred for most cases due to better signal handling and avoiding shell issues.</li>
</ul>
</ul>

<a href="https://docs.docker.com/reference/dockerfile/#entrypoint" target="_blank">Documentation and example code here</a></details>

## Analysing the dockerfile

<details close>
  <summary>Peeking inside the 'blackbox'</summary>
<br>
Our <a href src="https://hub.docker.com/r/rawworks/rawworks-cicd-toolset" target="_blank">example docker image</a> is created using [our `dockerfile` source hosted at Github](https://github.com/rawworks-nl/docker-cicd-azure-devops-agent/blob/main/Dockerfile).<br><br>
While dismembering this file line by line we can understand what is happening inside the container.<br><br>

Additional explanations are delineated within the four asterisks on either side.


<code>#### Use the latest version of the provided ubuntu image as base layer ####</code>

    FROM ubuntu:latest


<code>#### Ensuring the latest package repository update (note: no upgrade) ####</code>

    ENV DEBIAN_FRONTEND=noninteractive
    RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes
    RUN apt-get update


<code>#### installing tools to manage the sofware repositories ####</code>

    RUN apt install software-properties-common


<code>#### Adding the Ansible repository to the /etc/apt/sources.d/ directory ####</code>

    # RUN add-apt-repository ppa:deadsnakes/ppa
    RUN add-apt-repository --yes --update ppa:ansible/ansible


<code>#### Update repository cache and when succeeded install required software packages. When the above was succesfull the apt cache will be removed ####</code>

    RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        jq \
        git \
        iputils-ping \
        libcurl4 \
        libunwind8 \
        netcat \
        libssl1.0 \
    #    python3.8 \
        python3 \
        python3-pip \
        ansible \
        wget \
        apt-transport-https \
    && rm -rf /var/lib/apt/lists/*


<code>#### Download the Azure CLI and install it, remove the apt cache ####</code>

    # Azure CLI
    RUN curl -LsS https://aka.ms/InstallAzureCLIDeb | bash \
    && rm -rf /var/lib/apt/lists/*


<code>#### Install the Python module for WinRM functionality ####</code>

    # WinRM for Ansible
    # RUN pip install "pywinrm>=0.3.0"
    RUN pip install pywinrm


<code>#### Retrieve the Azure requirements for the forced installation of the azure.azcollection ####</code>

    # Ansible and the azure requirements
    RUN curl https://raw.githubusercontent.com/ansible-collections/azure/dev/requirements-azure.txt --output requirements-azure.txt
    RUN pip install -r requirements-azure.txt
    RUN ansible-galaxy collection install azure.azcollection --force

<code>#### Retrieve the Hashicorp Packer and Terraform ####</code>

    # Hashicorp products (Packer & Terraform)
    RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | apt-key add -
    RUN add-apt-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
    RUN apt-get update
    RUN apt-get install packer terraform

<code>#### Retrieve the Microsoft Powershell .DEB package ####</code>

    # Microsoft PowerShell
    RUN wget -q https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
    RUN dpkg -i packages-microsoft-prod.deb

    RUN apt-get update
    RUN apt-get install powershell

<code>#### Clean up the obsolete packages ####</code>

    # Clean up

    RUN apt autoremove --purge
    RUN apt clean

    RUN df -h

<code>#### setting up the Azure DevOps Agent ####</code>

    # Azure DevOps Agent
    ARG TARGETARCH=amd64
    ARG AGENT_VERSION=2.194.0

    WORKDIR /azp

    RUN if [ "$TARGETARCH" = "amd64" ]; then \
        AZP_AGENTPACKAGE_URL=https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-x64-${AGENT_VERSION}.tar.gz; \
        else \
        AZP_AGENTPACKAGE_URL=https://vstsagentpackage.azureedge.net/agent/${AGENT_VERSION}/vsts-agent-linux-${TARGETARCH}-${AGENT_VERSION}.tar.gz; \
        fi; \
        curl -LsS "$AZP_AGENTPACKAGE_URL" | tar -xz

<code>#### Copy the entry script to the workdir inside the containter and set execute permissions ####</code>

    COPY ./start.sh .
    RUN chmod +x start.sh

<code>#### Execution of the actual script of applications inside the docker ####</code>

    ENTRYPOINT [ "./start.sh" ]
</details>

## Building the container from the dockerfile

Manufacturing the container is easy however can take a while. Navigate to the directory containing the docker source files.

Execute the <a href src="https://docs.docker.com/reference/cli/docker/image/build/" target="_blank"><code>build</code> command</a>:

### Development build stage

For the development/experimental build it's usefull to have the complete logs, set the <code>--progress=plain</code> parameter.

```clear && docker build -t containername:tagname . --progress=plain``` 

```clear && docker build -t containername:tagname . --progress=plain --no-cache``` 

### Final build stage

For the final build clean base-image cache and add the <code>--no-cache</code> parameter.

```clear && docker build -t myfirstcontainer:myfirsttag .``` 

Note that the <code></code> doesn't refresh the base-image, <code>ubuntu:latest</code> in our case.

docker run - test 

Tagging

<a href="https://docs.docker.com/reference/cli/docker/image/build/" target="_blank">Documentation and example code here</a></details><br>

## Check the container images

See if the images is added locally

```docker image ls```

RUN



## Pushing the container to the docker registry

docker-desktop push

## Using the container

docker pull

## Reference material:

* [Official documentation on dockerfile](https://docs.docker.com/reference/dockerfile/)

* [Best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

* [Blog: dockerfile efficiency](https://www.docker.com/blog/intro-guide-to-dockerfile-best-practices/)

* [Docker Forum](https://forums.docker.com/)

* [Docker community on Slack](https://dockercommunity.slack.com/)