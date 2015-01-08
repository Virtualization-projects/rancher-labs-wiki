## Agents and services in Rancher
Outside of the [[cattle orchestration engine|https://github.com/rancherio/cattle]], there are a number of agents and other services that perform core operations in Rancher. These include the:
* [[python-agent|https://github.com/rancherio/python-agent]]
* [[console-agent|https://github.com/rancherio/console-agent]]
* [[node-agent|https://github.com/rancherio/node-agent]]
* [[api-ui|https://github.com/rancherio/api-ui]]

And there are more to come. These each serve a different function and are written in the programming language most suited to the task. 

## python-agent and cattle communication
Perhaps the most important (or containing the most crucial functionality) is the python-agent. It is the bridge between Rancher and Docker. It translates Rancher API requests to docker commands. Here's a very high-level view of the interactions between these components, using container creation as an example:

1. The user requests an new container in the UI.
1. The UI calls the Rancher API with a POST to /containers with a json payload that describe the type of container the user wants.
1. The API creates a container entity and its workflow is kicked off. Ultimately, this leads to an instance.activate event being sent to a particular python-agent running on a particular host. This event tells the python-agent to create and start the container.
1. The python-agent translates the event payload into arguments that the [[docker-py library|https://github.com/docker/docker-py]] understands.
1. The library executes the commands, calling the Docker API running on the host and reports the result.
1. Python-agent parses the response, possibly gathers some additional information, and replies to the original event sent down to it from cattle.
1. Cattle receives the response event and updates the resource in Rancher accordingly.

## Setting up python-agent locally
So, to the task at hand: how do you work on python-agent? How do you coordinate the work needed in cattle with the work needed in python-agent?

First off, let's understand how you can run and use a local version of python-agent with your locally running cattle server. The rancher-agent container is responsible for running the python-agent code. But that code isn't packaged with the agent. It is pulled down dynamically from cattle when the container is started (and on subsequent intervals). 

This property determines where cattle looks for the latest python-agent code:
[[agent.package.python.agent.url|https://github.com/rancherio/cattle/blob/58eb6235a01a27695d05aada93642d8c96654636/resources/content/cattle-global.properties#L8]]. The master branch of cattle will always point to an official released version of the code but you can override that property to point to a directory on your local machine. To do so, add the following to the ***VM arguments*** section of you cattle debug configuration in eclipse:
```
-Dagent.package.python.agent.url=/path/to/your/projects/python-agent
-Dtask.config.item.source.version.sync.schedule=5
-Dtask.config.item.migration.schedule=5
```
The next two properties cause cattle to check your local python-agent directory for changes every 5 seconds and to push those changes into the agent every 5 seconds.

Let's test that out. Here's the full step by step:

1. Fork, clone, and branch python-agent
1. ```cd``` to the python-agent directory and run ```./scripts/build```. This is a one-time* operation that performs some configuration and building of the project. **Unless you do something that causes you to need to rebuild, like change the python dependencies of the project or blow away the dist dir*
1. Add the above mentioned properties to the eclipse debug config and start cattle.
1. In a new terminal window, tty into the python-agent container: ```docker exec -it rancher-agent bash```.
1. In the tty:
 * ```ls /var/lib/cattle/``` Two important things to note: there's an **agent.log** file to which the python-agent logs. The **pyagent** dir is where the pyhton-agent code is deployed.
1. Jump into a different terminal window and get into your python-agent directory. Run ```touch cattle/foobar```
1. Back in the tty, ```ls /var/lib/cattle/pyagent/cattle``` and after 10-20 seconds you should see the foobar file. Note that you may have been disconnected from the container as it restarted. If so, you may need to tty in again.

So, that's how code gets pushed into the rancher-agent container. Here's a good exercise: add a log statement to the [[docker instance activate function|https://github.com/rancherio/python-agent/blob/master/cattle/plugins/docker/compute.py#L178]] and then tail the logs to find it. You can either tty into the container and tail /var/lib/cattle/agent.log or you can run ```docker logs -f rancher-agent```. Note that there is currently a problem with running cadvisor in boot2docker. So, the logs may be chatty with errors around cadvisor. You can filter that out via grep. If using the ```docker logs``` approach, errors come over stderr, so you'd need to redirect stderr to stdout.

## Coordinating work between python-agent and cattle
As you can see, cattle and python-agent are two discrete services. You may be asking yourself how to coordinate work on the two. Here's a good approach (though just a suggestion):

First, identify how Cattle and python-agent will communicate the data that your feature needs.
 * Is the data already in the event being sent down?
 * Is it just a new property or two on an existing event?
 * Do you need to send down an entirely new event?

Second, get the functionality working and tested in python-agent. Code in python-agent ***must*** be tested (and testable) independent of cattle. So, the tests in python-agent rely basically on sending and receiving mock events to the actual agent code. Note that while these tests have no dependency on cattle, they still are not true unit tests because they can still cause the code to communicate with docker or the host file system. The mock events are in the [[tests/docker|https://github.com/rancherio/python-agent/tree/master/tests/docker]] directory. There's a small framework for working with these events. [[test_docker.py|https://github.com/rancherio/python-agent/blob/master/tests/test_docker.py]] makes heavy use of this framework; just look for the event_test() function.

Third, make the necessary changes in cattle. You may need to create new properties on an API resource or add/modify a process handler to send additional data down in an event. Write integration tests that exercise the new functionality. There are two types of integration tests you should be concerned with:
 * Tests that rely on simulators to prevent the need to go all the way to python-agent (and thus docker). Simulators are basically heavy mocks that will simulate the interactions with python-agent. For examples, just search for "sim" in the integration tests projects or look for java classes containing the word Simulator in cattle. ```SimulatorPingProcessor``` is a useful example.
 * Tests that rely on actually going to python-agent and docker. These tests will let you exercise your code from end-to-end and across cattle and python-agent. Check out [[test_docker.py|https://github.com/rancherio/cattle/blob/master/tests/integration/cattletest/core/test_docker.py]] in the integration tests project for such examples.

Simulator tests should be used to cover all major code paths in cattle. End-to-end tests should be used more sparingly to verify the general communication between cattle and python-agent. The tests you wrote in python-agent should be used to thoroughly test all code paths in python-agent; you shouldn't rely on cattle integration tests for that.

Finally, here's how we'll coordinate committing the code:

1. You submit a PR for python-agent when you're ready for feedback. When it's good-to-go, we'll merge that PR. Code in python-agent master must always be backwards compatible because we will cut a release for it first and cattle may start referencing that release before your cattle PR has been merged.
1. Get feedback for you cattle changes. Note that we run CI against all PRs as soon as they're opened and whenever they're updated. So a chicken and egg scenario may emerge: if your cattle PR relies on functionality in a new release of python-agent, you cattle PR will fail CI, but you want to get feedback sooner than that. Here are some things we do to work around that:
 * You can open a PR in your own fork (from your own feature branch to your own master branch) and explicitly ask for feedback on it. This is good for early feedback when you don't feel that you're terribly close to getting your code merged to master of rancherio/cattle
 * You could open the PR as normal against rancherio/cattle and just note in a comment that CI will fail until python-agent version is updated in cattle. This approach is good when you're close to merging and specifically when the python-agent changes are fully-baked, possibly even merged to master in that project.
1. When a new release of python-agent with your changes has been cut, update the version that cattle points to in the [[global.properties|https://github.com/rancherio/cattle/blob/58eb6235a01a27695d05aada93642d8c96654636/resources/content/cattle-global.properties]] file add it your cattle PR. At this point, CI should *-and must-* pass. If all is good, the cattle PR will get merged as well.

A random note on commits in PRs: while you don't have to squash everything single commit as you are woking on your feature, once you are ready for the PR to be merged, you should squash all your commits down to one. This will allow our history to be clean and meaningful with roughly a 1:1 ratio of commits to issues.