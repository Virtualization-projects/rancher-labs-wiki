Container fields define runtime behaviors of a container. Things like `workingDir` and `securityOpts` define the  runtime parameters of a container. Cattle already supports a list of container fields. In this Wiki, we will go through the framework and explain how to add a new field in cattle.

To add a field in cattle, we need to change four projects in rancher. Those are [rancher/agent](https://github.com/rancher/agent), [rancher/cattle](https://github.com/rancher/cattle), [rancher/rancher-compose-executor](https://github.com/rancher/rancher-compose-executor) and [rancher/go-rancher](https://github.com/rancher/go-rancher). Noticed that this instruction only applies to rancher v1.6.x.

In the following example, we will take `init` as an example to explain how we can add it into cattle.

1. Submit a PR to rancher/agent like https://github.com/rancher/agent/pull/172. Agent receives event from cattle and launch containers through docker in each host. We need to make sure that agent can take the request and pass the `init` to docker API(which is config and hostConfig). Make sure to add an integration test for the new field.

2. Once agent is merged and released, we need to update cattle(https://github.com/rancher/cattle/pull/2823). One thing we need to remember is to bump agent version(https://github.com/rancher/cattle/pull/2831). So basically in cattle there is a test file called `test_docker.py` which uses the new agent binary to launch docker containers. Without new agent release the test won't pass even the new field is added into the schema. After that, we need to do the following changes:

    a) update `container.json` and `user.auth` to add new field to the schema. Add tests in `test_authrization.py` to test the schema. Also update `test_docker.py` to test new fields. In UI `localhost:8080/v2-beta/projects/1a5/instances` you can click on the button to create container, and then you can check if the new field is being added to the schema.

    b) update `ServiceDiscoveryConfigItem.java`. This is required when we transform service api to YAML compose file. Update the corresponding test file `test_svc_discovery.py`. 

    c) update `DockerTransformerImpl.java`. This is required when rancher-compose-executor call back cattle to transform `config` and `hostConfig` to service.launchConfig. 

3. Once Cattle PR is merged, update go-rancher(https://github.com/rancher/go-rancher/pull/161). Go-rancher is our golang binding for all cattle resources. This will make sure the new fields is added in go-rancher.

4. Once Go-rancher PR is merged, update rancher-compose-executor(https://github.com/rancher/rancher-compose-executor/pull/67). This change is required to support new fields in compose file. Follow the same pattern and don't forget to add the tests!

5. Update Cattle with new rancher-compose-executor release. (https://github.com/rancher/cattle/pull/2840)

6. (Optional) Bump the CLI with new rancher-compose-executor code. Because CLI uses rancher-compose-executor's code base, it needs to update vendor library. (https://github.com/rancher/cli/pull/70)

Last thing, make sure the new field is not conflicted with any javaScripts or Ember function. In this example the  `init` is conflicted with a ember init function and we end up with renaming it as `runInit`. That's too much work as you need to redo all of these above!