# Lab 1 - Getting started

This lab is going to let you prepare your development environment for the rest of the labs in the workshop. Not all steps are required. The more you can prepare, the better the experience during the workshop.

Goals for this lab: 
- [Prepare development laptop](#1)
- [Download required and optional tooling](#2)
- [Clone Git repository for lab code](#3)
- [Prefetch Docker images](#4)
- [Create Docker cluster on Microsoft Azure](#5)
 
## <a name="1"></a>1. Prepare your development laptop
Make sure that your laptop is up-to-date with the latest security patches. This workshop is specific towards Windows as the operating system for your machine. The labs can also be done on Linux, although this can be a bit more challenging.

## <a name="2"></a>2. Download required and optional tooling
First, you will need to have a development IDE installed. The most preferable IDE is [Visual Studio 2019](https://www.visualstudio.com/vs/) if you are running the Windows operating system.

You should also install [Visual Studio Code](https://code.visualstudio.com/) for the Angular labs and as an alternative to Visual Studio 2019 the following cases:
- Your development machine is running OSX or a Linux distribution as your operating system.
- You want to have an light-weight IDE or use an alternative to Visual Studio 2019.

> Download and install either [Visual Studio 2019](https://www.visualstudio.com/downloads/) or [Code](https://www.visualstudio.com/downloads/).
>
> For Visual Studio Code, also install the [Kubernetes](https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools) and [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) extensions.

Second, you are going to need the Docker Desktop Community Edition tooling on your development machine. Depending on your operating system you need to choose the correct version of the tooling. Instructions for installing the tooling can be found [here](https://docs.docker.com/install/). You can choose either the stable or edge channel.

> Download and install Docker Community Edition:
> - [Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)
> - [Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/install/)

You will also need the [Azure Command Line 2.0 tooling](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) for interaction with Azure resources. 
> Install [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest)

Download and install [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) if needed.

The following optional tools are recommended, but not required.

- [GitHub Desktop](https://desktop.github.com/) for Git Shell and Git repository utilities
- [PuTTY](http://www.putty.org/) for `PuTTY.exe` and `PuTTYgen.exe`

Download and install NodeJS from https://nodejs.org/en/

You need this to be able to run `npm` commands. With this you can also manage the packages of the project.

Install the Angular CLI by running this npm command from a console window:
```sh
npm install -g @angular/cli
```

## <a name="3"></a>3. Clone Git repository for lab code
The workshop uses an example to get you started with Dockerizing a typical ASP.NET Core application. 
The application is themed around high-scores of retro video games. It consists of web front end and a Web API and stores high-scores in a relational database.

Clone the repository to your development machine:
- Create a folder for the source code, e.g. `C:\Sources\Workshop`.
- Open a command prompt from that folder
- Clone the Git repository for the workshop files:

```
git clone https://github.com/XpiritBV/BuildingModernWebApplicationsWorkshop.git
```
- Set an environment variable to the root of the cloned repository from PowerShell:
```
$env:workshop = 'C:\Sources\Workshop'
```
## <a name=""></a> Prefetch Docker images
To avoid downloading large images during the workshop, you can pull the images for the labs ahead of time:
Execute the following commands from a command-line window:

```sh
docker pull mcr.microsoft.com/mssql/server
docker pull mcr.microsoft.com/dotnet/core/runtime:3.0-buster-slim
docker pull mcr.microsoft.com/dotnet/core/aspnet:3.0-buster-slim
```

## <a name="5"></a>5. (Optional) Create a Kubernetes cluster in Azure

This part requires you have an active Azure subscription. If you do not, you can create a trial account at [Microsoft Azure](https://azure.microsoft.com/en-us/free/). It will require access to a credit card, even though it will not be charged.

First, login to your Azure subscription using the Azure CLI and switch to the right subscription (in case you have multiple subscriptions). The second command will list your subscriptions. Choose the appropriate GUID to select the subscription you want to use and substitute that in the third command. 

```
az login
az account list -o table
az account set --subscription <your-subscription-guid>
```

After you have successfully logged in, create a resource group for your cluster.

```
az group create --location WestEurope --name Workshop
```

Install the Azure Kubernetes Service Command-Line Interface tools by running and following the instructions from this command:
```
az aks install-cli
```
Next, create a service principal to manage the Azure subscription. Store the information that is displayed after creation:

```
az ad sp create-for-rbac --name "WorkshopServicePrincipal" --skip-assignment
```
The result should look similar to this:

``` json
{
  "appId": "71860f73-7e41-4863-afca-01acaaaa9cd4",
  "displayName": "azure-cli-2019-5-26-15-25-53",
  "name": "http://azure-cli-2019-5-26-15-25-53",
  "password": "7ae238c0-1bdb-4df7-bf27-29091ed48dfa",
  "tenant": "1b2bfa88-825c-4d4e-b258-5ae208c0aafa"
}
```

You can create the cluster with the `az aks create` command. You need to tweak the command below to contain the specifics from the created service principal. You can also change the names of the cluster and DNS name prefix to your liking:
```
az aks create --resource-group Workshop --name WorkshopCluster 
  --dns-name-prefix workshop 
  --client-secret <your-principal-password> 
  --service-principal <your-principal-appid> 
  --generate-ssh-keys --location westeurope --node-count 3 
  --kubernetes-version 1.13.5 --max-pods 100 
  --enable-addons http_application_routing
```

After the cluster has been created, check whether it is up and running by opening the Kubernetes Dashboard:
```
az aks get-credentials --resource-group Workshop --name WorkshopCluster -a
az aks browse --resource-group Workshop --name WorkshopCluster
```

You might get errors in the home page of Role Based Access Control (RBAC) is on by default for the latest version of AKS.
For now, you will fix the RBAC access with by running the following command from the root folder of your cloned Git repository:
```
kubectl create -f dashboard-admin.yaml
```

If all is correct, the Kubernetes dashboard can be launched from your default browser without any errors now.

This does not incur any costs other than your Azure resource consumption and should be fit easily within your Azure trial subscription credits.

## Wrapup
You have prepared your laptop and cloud environment to be ready for the next labs. Any issues you may have, can probably be resolved during the labs. Ask your fellow attendees or the proctor to help you, if you cannot solve the issues.

Continue with [Lab 2 - Creating Web API](Lab2-CreatingWebAPI.md).