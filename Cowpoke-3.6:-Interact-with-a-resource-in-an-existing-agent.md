Outside of the [[cattle orchestration engine|https://github.com/rancherio/cattle]], there are a number of agents and other servicesthat make perform core operations in Rancher. These incldue the:
* [[python-agent|https://github.com/rancherio/python-agent]]
* [[console-agent|https://github.com/rancherio/console-agent]]
* [[node-agent|https://github.com/rancherio/node-agent]]
* [[api-ui|https://github.com/rancherio/api-ui]]

And there are more to come. These each serve a different function are written in the programming language most suited to the task. Perhaps the most important (or containing the most crucial functionality) is the python-agent. It is the bridge between Rancher and Docker. It translates Rancher API requests to docker commands. Here's a very high-level view of the interactions between these components, using container creation as a use case:

1. The user requests an new container in the UI.
1. The UI calls the Rancher API POST to /containers and a json payload describe the type of container desired.
1. The API creates container entities and its workflow is kicked off. Ultimately, this leads to a instance.activate event being sent to a particular python-agent running on a particular host. This event tells the python-agent to create and start the container.
1. The python-agent translates the event payload into arguments that the [[docker-py library|https://github.com/docker/docker-py]] understands.
1. The library executes the commands, calling the Docker API running on the API and reports the result.
1. Python-agent parses the response, possibly gathers some additional information, and replies to the original event sent down to it.
1. Rancher receives the response event and updates the resource in Rancher accordingly.

So, to the task at hand: how do you work on python-agent? How do you coordinate the work need in cattle with the work needed in python-agent.

First off, let's understand how you can run and use a local version of python-agent with your locally running cattle server. The rancher-agent container is responsible for running the python-agent code. But that code isn't pacakged with the agent. It is pulled down dynamically from cattle when the container is started (and on subsequent configurable intervals). This property determines where cattle looks for the latest python-agent code:
The [[agent.package.python.agent.url|https://github.com/rancherio/cattle/blob/58eb6235a01a27695d05aada93642d8c96654636/resources/content/cattle-global.properties#L8]] controls where cattle looks for the python-agent code. The master branch of cattle will always point to an official released version of the code but you can override that property to point to a directory on your local machine. To do so, add the following to the ***VM arguments*** section of you cattle debug configuration in eclipse:
```
-Dagent.package.python.agent.url=/path/to/your/projects/python-agent
-Dtask.config.item.source.version.sync.schedule=5
-Dtask.config.item.migration.schedule=5
```
If you haven't forked and cloned python-agent yet, you'll need to do that and set the first property to where you cloned the project. The next to properties cause cattle to check your local python-agent directory for changes every 5 seconds and to push those changes into the agent every 5 seconds.

Let's test that out. Add those config properties and restart cattle. 




Here's a good approach:

Identify how Cattle and python-agent will communicate the data that your feature needs.
* Is the data already in the event being sent down?
* Is it just a new property or two on an existing event?
* Do you need to send down an entirely new event?
