# Chaos Engineering

#### Definition

Chaos Engineering is the discipline of experimenting on a system in order to build confidence in the system's capability to withstand turbulent conditions in production environment

#### Before we start

In order to implement chaos engineering experiments, we have to do it:

* In production environment
* Get enough time to understand your application and its dependencies
* Have observability on the system (Monitoring - Alerting - Tracing ...)

#### Terminology

* Experiment: An experiment is a sequence of tasks to apply on the systems in order to destroy parts of it
* Steady-state hypothesis: The steady state is the state of the system before and after running the experiment, if they match, we conclude that our system is resilient and highly available

#### Process

* Create your experiment
* Define the steady-state hypothesis 
* Run the experiment 
* Create reports

#### Tools

In this section, we will demonstrate a list of most powerful tools in the market, and we will choose our weapon.

| Tool              | Scope        | Documentation | Open-source/Free | Community |
|-------------------|--------------|---------------|------------------|-----------|
| Manual Chaos      | All          | None          | Yes              | None      |
| Customize scripts | All          | None          | Yes              | None      |
| Chaos Monkey      | AWS          | Good          | Yes              | Good      |
| Simian Army       | AWS          | Good          | Yes              | Good      |
| Gremlin           | Wide         | Good          | No               | Good      |
| Powerful Seal     | K8s          | Poor          | Yes              | Poor      |
| kube-monkey       | K8s          | Poor          | Yes              | Poor      |
| Litmuschaos       | K8s          | Poor          | Yes              | Poor      |
| Glooshot          | Service Mesh | Poor          | Yes              | Poor      |
| Chaos toolkit     | Wide         | Good          | Yes              | Good      |

After comparing all available tools, Chaos toolkit seems to be the complete tool to choose, it can be used for different components, it's free, open source, and have good documentation

#### Setting up the environment

The following tools needs to be installed before we start experimenting on our system:

* Git
* Helm
* Python3
* Pip

#### Installing chaos toolkit

Install python3

On Debian like systems

```
# apt install python3 python3-env
```

On RHEL systems

```
# yum install python3
```

Create a python virtual environment

```
# python3 -m venv ~/venvs/chaostk
```

Install chaostoolkit binary

```
# cd ~/venvs/chaostk && ./bin/pip3 install -U chaostoolkit
# cp ./bin/chaos /usr/local/bin/
```

To test if chaos toolkit is installed

```
# chaos version
```


