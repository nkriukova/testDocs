## INTRODUCTION
In this guide we will go through the simple steps that would help to start with th2, based on the demo example. If you are already familiar with the th2 paradigm and interested in installing th2 for your business needs, refer to our [th2 technical requirements](https://github.com/th2-net/th2-documentation/wiki/Technical-Requirements) page to check which th2 configuration will be suitable for you.

These steps require you to have a cluster running a compatible version of Kubernetes. It's possible to use any supported platform, for example Minikube or install it locally, guide for kubernetes installation on Centos-7 is available in the [FAQ section](https://github.com/th2-net/th2-documentation/wiki/Centos-7-kubernetes-and-cassandra-installation-guide).

More information regarding the use cases covered in the demo example, it's configuration, execution process and results' verification can be found on our [th2 demo example page](https://github.com/th2-net/th2-documentation/wiki/Demo-Example).

## GETTING STARTED WITH th2
**Follow these steps to get started with th2:**
1. [Install required software to the **test-box** and **operator-box**](https://github.com/th2-net/th2-documentation/wiki/Getting-Started/_edit#install-required-software)
2. [Download th2 to your Git repository](https://github.com/th2-net/th2-documentation/wiki/Getting-Started/_edit#download-th2-to-your-git-repository)
3. [Download th2 **Demo Example** configuration to your Git repository](https://github.com/th2-net/th2-documentation/wiki/Getting-Started/_edit#download-th2-demo-example-to-your-git-repository)
3. [Set up your cluster and Install th2](https://github.com/th2-net/th2-documentation/wiki/Getting-Started/_edit#set-up-your-cluster-and-instull-th2)
4. [Create your first th2 custom environment in a dedicated namespace](https://github.com/th2-net/th2-documentation/wiki/Getting-Started/_edit#create-your-first-th2-custom-environment)
5. [Get and Run **th2-script-demo**](https://github.com/th2-net/th2-documentation/wiki/Getting-Started/_edit#get-and-run-demo-script)


## INSTALL REQUIRED SOFTWARE
th2 is a Kubernetes-driven microservices solution. So, to start with th2 you would need the fully functional Kubernetes cluster installed on your test-box/test-boxes, please also check if your **test-box** and **operator-box** meets th2 software [th2 requirements](https://github.com/th2-net/th2-documentation/wiki/Technical-Requirements#software-requirements)

Minimal hardware th2 requirements for demo example:
* 6 CPU cores
* 16 GB RAM
* 100 GB disk space

If you would like to enhance demo configuration with additional boxes, please check [th2 technical requirements](https://github.com/th2-net/th2-documentation/wiki/Technical-Requirements).

## DOWNLOAD th2 TO YOUR GIT REPOSITORY
th2 components should be copied to your Git repository if you would like to try th2 custom componets, modify them based on your business logic or evaluate th2 onboarding process. If, at this step, you do not want to make any changes,but just run demo example without any modifications, this step can be skipped. 

You would need the following:
- [th2-infra](https://github.com/th2-net/th2-infra) for core platform
- **th2 custom**, components from the demo example:
     - [th2-recon-template](https://github.com/th2-net/th2-check2-recon-template)
     - [th2-grpc-sim](https://github.com/th2-net/th2-grpc-sim-template)
     - [th2-sim](https://github.com/th2-net/th2-sim-template)
     - [th2-grpc-act](https://github.com/th2-net/th2-grpc-act-template)
     - [th2-act-template](https://github.com/th2-net/th2-act-template-j)
## DOWNLOAD th2 DEMO EXAMPLE TO YOUR GIT REPOSITORY

Get [th2-infra-schema](https://github.com/th2-net/th2-infra-demo-configuration) for custom components and copy or fork to your GIT repository.

## SET UP YOUR CLUSTER AND INSTULL th2
Details regarding specific cluster setup, required for th2 installation process are available in th2-infra [README.md](https://github.com/th2-net/th2-infra) file.

## CREATE YOUR FIRST th2 CUSTOM ENVIRONMENT
In th2, environment is called `infractructure schema` or just `schema`, it's created by the dedicated [infra-mgr](https://github.com/th2-net/th2-infra-mgr) component that watches for changes in the repositories and rolling out schemas from git repository to kubernetes. Please refer to [infra-schema-demo](https://github.com/th2-net/th2-infra-schema-demo) instruction to create your first th2 environment.


## GET AND RUN DEMO SCRIPT
Copy [th2-script-demo](https://github.com/th2-net/th2-demo-script) example to your GIT and run it based on the following instruction:

**Steps:**
1. Copy to your repository content from the [link](https://github.com/th2-net/th2-demo-script)
2. Get python environment 3.7+ (e.g. conda).
> Recommended: get IDE to work with python (e.g. pycharm, spyder). You can also start this script from the command line, but IDE will make this process more convenient.
3. Import the libraries described in **requirements.txt**
> requirements.txt contain standart packages to work with grpc (e.g. google-api-core) and custom packages to work with th2 boxes. Please note that grpc client (script) and grpc server (th2 box) should use the same package. You can find more information about `requirements.txt` and package installation here: https://pip.pypa.io/en/stable/user_guide/#requirements-files
4. Set up configs from directory configs (`mq.json`, `rabbit.json`, `grpc.json`) according to your components. 
* Fill `grpc.json` in folder config with host and external port of your act and check1 pods. You can found it in Kubernetes Dashboard in Services tab or execute in kubectl - kubectl get services
* Fill `mq.json` in folder config with RabbitMQ exchange and routing key from script-entry-point to estore. You can find this queue in Kubernetes Dashboard in Config Maps tab - script-entry-point-app-config.
* Fill `rabbit.json` in folder config with your RabbitMQ credentials. You can find this credentials in Kubernetes Dashboard in Config Maps tab - rabbit-mq-app-config.
5. Run `AgressiveIOC_Traded_against_TwoOrders_partially_and_Cancelled.py`.

