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

## Experimenting kubernetes

Before starting our experiments, we have to deploy a chaostoolkit driver called 'chaostoolkit-kubernetes'

```
# cd ~/venvs/chaostk && ./bin/pip3 install -U chaostoolkit-kubernetes
```

To list all the tasks that this plugin can perform, we will use the 'discover' option

```
# chaos discover chaostoolkit-kubernetes
```

Or reffer to the following link:

[chaostoolkit-kubernetes Docuentation](https://docs.chaostoolkit.org/drivers/kubernetes/)

#### Testing Controllers

* First of all, we will test if our application is deployed using kubernetes controllers by terminating application instances

Note: Before running our experiment, we have to define the steady state of our application

In the following experiment, we have six sections:

* Version: Current version of chaostoolkit API 
* Title: Title of our experiment
* Description: Description of our experiment
* Tags: Used to organize experiments
* Steady-state-hypothesis: The state of our application, in this case, we are checking wether a pod exists or no
* Method: In the method section, we will terminate a random pod that has 'app=demo' label, and we will pause the expermient before checking wether the pod exists or no  

```
version: 1.0.0
title: What happens if we terminate a Pod?
description: If a Pod is terminated, a new one should be created in its places.
tags:
- k8s
- pod
steady-state-hypothesis:
  title: Pod exists
  probes:
  - name: pod-exists
    type: probe
    tolerance: 1
    provider:
      type: python
      func: count_pods
      module: chaosk8s.pod.probes
      arguments:
        label_selector: app=demo
        ns: demo
method:
- type: action
  name: terminate-pod
  provider:
    type: python
    module: chaosk8s.pod.actions
    func: terminate_pods
    arguments:
      label_selector: app=demo
      rand: true
      ns: demo
  pauses: 
    after: 10
```

- In all our examples, we will be using a demo called app, in a namespace called demo

- If the experiment fails, we have to use a controller to deploy our applications, kubernetes deployments is the ultimate solution to keep our application in an existing status.

- Checking wheter a pod exists or no is not enough in kubernetes, as you know, a pod may exists, and not be in a running state, so we will push our experiment further, and we will check if the pods are in a 'Running' state.

```
version: 1.0.0
title: What happens if we terminate a Pod?
description: If a Pod is terminated, a new one should be created in its places.
tags:
- k8s
- pod
steady-state-hypothesis:
  title: Pod exists
  probes:
  - name: pod-exists
    type: probe
    tolerance: 1
    provider:
      type: python
      func: count_pods
      module: chaosk8s.pod.probes
      arguments:
        label_selector: app=demo
        ns: demo
  - name: pod-in-phase
    type: probe
    tolerance: true
    provider:
      type: python
      func: pods_in_phase
      module: chaosk8s.pod.probes
      arguments:
        label_selector: app=demo
        ns: demo
        phase: Running
  - name: pod-in-conditions
    type: probe
    tolerance: true
    provider:
      type: python
      func: pods_in_conditions
      module: chaosk8s.pod.probes
      arguments:
        label_selector: app=demo
        ns: demo
        conditions:
        - type: Ready
          status: "True"
method:
- type: action
  name: terminate-pod
  provider:
    type: python
    module: chaosk8s.pod.actions
    func: terminate_pods
    arguments:
      label_selector: app=demo
      rand: true
      ns: demo
  pauses: 
    after: 10

```
