## 1. INTRODUCTION
This is the example of how to use th2 test toolkit. In this example you will study how to install, set up and run th2. The demo script helps you to understand how to work with th2 and what functions it can provide. 
We’ll look at the example which will show how th2 modules cooperate with each other to perform test scenarios. We’ll see how the th2 script will generate and send the messages, get the response from the system, store and check test data.

## 2. INSTALLATION AND SETTING TH2
To start using TH2 you have to configure the Kubernetes cluster. You can find the hints on how to set up your Kubernetes <here>. 

After installing all the core components in the cluster Kubernetes, you can use the th2 demo configuration of the th2 environment.  

To set up custom parameters of th2 follow the steps:

1. Copy the content of [repository](https://github.com/th2-net/th2-infra-demo-configuration) to your git repository. For gitlab you can use the ‘Import project’ option to create a new repository.
2. Connect your new repository to `infra-mgr`. Please find the hints <here>.
3. To set up read-log follow next steps:
* Create the directory for storage `read-log` files;
* Specify the path to the directory in `config.yaml` file;
* Place the files from the [directori](https://github.com/th2-net/th2-read-log/tree/master/examples) to your directory.
4. Specify the parameter `k8s-propagation` in `infra-mgr-config.yml` file. Specify it as ‘rule’ or ‘sync’ for the `infra-mgr` to start the installation. Please note that namespace’s name with this configuration in Kubernetes will have the same name as the branch name and have the prefix `schema_`. 
> Note that `infra-mgr-config.yml` file content a list of possible parameters for `k8s-propagation` and parameter description.

As the result of these steps `infra-mgr` will create a new namespace in your Kubernetes and rise up all components from current configuration. The related namespace with all the necessary settings will be set up automatically with `infra-mng` after you’ll copy the config to your branch. In this configuration, you’ll be able to study how to work with th2 and also how to run the demo script. 

## 3. THEORY OF TH2
Let’s consider the components which we have in the demo configuration. 
1. We have a set of components which are responsible for the storage of information in cradle and to display them into the GUI: `estore`, `mstore`, `rpt-data-provider`, `rpt-data-viewer`.
2. A set of components which are responsible for creating outcoming messages and for checking messages: `act`, `check1`, `recon`, `util`.
3. We also have a set of components which receives messages from a remote system or other sources and convert them into a format that is understandable to the users and to other th2 components: `demo-conn1`, `demo-conn2`, `demo-dc1`, `demo-dc2`, `codec`, `read-log`.
4. A set of components that simulates another system (the provided set of messages is limited within the current demo script): `fix-demo-server1`, `fix-demo-server2`, `dc-demo-server1`, `dc-demo-server2`, `sim-demo`.

We also have a dictionary with the FIX protocol version, which is used by our components `conn` and `codec`. The `codec` will be used to encode/decode messages while the dictionary contains the description of version specific protocol messages. The `conn` component is used to communicate with the target system. A description with the connections between these components is represented in the diagram below:
![](https://drive.google.com/drive/folders/1xLqiRHizz5LQEUPA0_ZmmgkKwomimusq)

## 4. RUNNING THE SCRIPT

Now, let’s review our test scenario. Trader1 sends to the system two Buy Orders with different prices and quantities. After that, Trader2 will send an IOC Sell Order to the system with the price lower than both of the Trader1 Orders’ prices. We are going to check all the response messages, sent from the system, and the results of the trade. This scenario will be performed in several variations with different parameters.

To set up the script:
1. Copy to your repository content from the [link](https://github.com/th2-net/th2-demo-script)
2. Get python environment 3.7+ (e.g. conda).
> Recommended: get IDE to work with python (e.g. pycharm, spyder). You can also start this script from the command line, but IDE will make this process more convenient.
3. Import the libraries described in requirements.txt;
4. Set up configs from directory configs (`mq.json`, `rabbit.json`, `grpc.json`) according to your components. 
* `grpc.json` describes access to components act, check1.
* `rabbit.json` describes access to rabbitmq.
* `mq.json` describes queues used in rabbitmq.
> All required parameters you can find in Kubernetes. Instruction about these parameters you can find in the corresponding files.
5. Run `AgressiveIOC_Traded_against_TwoOrders_partially_and_Cancelled.py`.

The script represents the set of sending messages to the system and getting the responses from the system.

When sending the message, script sends a grpc request to the `act` component with instructions which message in which connector have to be sent. Act transfers the message to the `conn` client component. Then, based on the used grpc call, it starts to find the message which will be the response from the system on the message we’ve sent.

The `conn` client component gets the th2 message from the `act`, forms the FIX message based on a dictionary and then sends it to the `conn` server on FIX protocol. 

The `sim` gets this message from the `conn` server and creates a response on it, simulating remote system behavior. 

The response returns on the `conn` server and then transfers to the `conn` client on FIX protocol. Then response goes to the `codec`, where it’s decoded into human readable th2 format which is also clear to the other components.
From the codec all the messages come to the `act`, to the `check1` for verifying on requests from script and to the `recon` for passive verification.

When checking, the script sends a grpc request to `check1` with instructions on messages verification. This instructions content expected result on each message we want to verify. 

Also component `recon` performs the passive verification during all the env work.

## 5. ANALYSIS OF RESULTS

TH2 has a web-based GUI, which helps to manage and analyze test data. The GUI is divided into two parts: Events and Messages.

On the Event tab you can see the list of executed scripts. Each script has a tree of actions which is called events. Events may contain different data: information about sending messages, incoming messages, comparison tables, where you can check that expected and actual results match or don’t match. 

For our example, you can see all executed steps. First of all we request security status for instrument. Then we send Orders from Traders, get the responses from the system and compare them with the patterns. After that we check the system responses about the trade result. As the outcome we can see, that all tests passed, which means that the system works as expected.

Now let’s look at the 6th Case. As you can see it’s red, meaning that scenario failed in this case. It happened because we try to send orders on INSTR6, which doesn’t exist in the sim system. This example shows how th2 works with unexpected behavior of systems. It informs us that we find the bug.

The Message tab is the list of outcoming and incoming messages. It is linked with Events. When you choose an event with a message, this message is displayed on the list in the Message tab. Also you can navigate through the message list without reference to any event for extra analysis. 

Events and messages are stored in estore and mstore without time limits, so you can return to your test scenarios anytime. 
The last point of our example is the recon scenario. For recon we use several rules, which compares the data from different sources. We compare ExecutionReports from DEMO-CONN1 and DEMO-CONN2 with the original reports, ExecutionReports from FIX conn with Reports from DropCopy conn. Also we check messages in read-log and instruments in refData.

