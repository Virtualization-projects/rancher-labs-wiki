## Overview

Configuration Framework in Cattle handles config updates for the agent's hosts and network-agent instance.

Each configuration is represented by **configItem** having its own logic for config file generation, and that item gets assigned to a particular host/network-agent. Bumping up the version for the config item would trigger ConfigUpdate event for the host/network-agent, and agent would process this event by:

* comparing the current version of the config with the requested one
* and if the requested version is higher, request the fresh config from the Cattle server
* cattle server will generate the fresh config upon the request, that agent will apply on the host, or - if the config 

Existing examples of config-items:

`dnsmasq`
`ipsec-hosts`
`iptables`
`haproxy`

More details on how to introduce new config item in cattle, is in the next section

## How to add a new config item to cattle

1) Create a folder for your new config item under:

`$CATTLE_HOME/resources/content/config-content`

This folder will contain:

* Scripts performing for config update on the agent side. Those scripts will get packaged and installed upon python-agent/network-agent startup. Refer to any existing folder under config-content to see what directory structure should look like.

* Config file template (*.ftl file)

2) Create a class extending AbstractAgentBaseContextFactory under

`$CATTLE_HOME/code/iaas/config-item/server/src/main/java/io/cattle/platform/configitem/context/impl/<configItemName>InfoFactory.java`

This class will be responsible for setting the values needed by *.ftl config file to generate the data.

3) Specify the config item in config-items-default.properties in a following format:

`item.context.<configItemName>.info.items=<configItemName>`


4) In the config file template *.ftl, write up the logic for generating the config data following Free Marker Template language rules.

5) Write the logic for config item update. Good example of that logic can be found in AgentScriptsApply.java

## How to test your changes

The example below is for config item added for the network-agent. Testing for the host's config item should be similar.

There are 2 parts that can be tested separately:

1) Generating the config file and applying it on the network-agent
2) Config item update logic

To test 1), just build the config item and put in the logic to populate the config *ftl file. Then login to the network-agent and execute the script:

`cd /var/lib/cattle`
`./config.sh --force <configItemName>`

`example: ./config.sh —force haproxy`

After that, verify that the config file was generated and applied correctly

To test 2), invoke the cattle logic generating configUpdate request, and see if the **cattle.config_item_status.requested_version** field was incremented by one.

Then of course, test 1) and 2) in conjunction - ConfigUpdate request should trigger config item increment, and that in turn should trigger script apply on the network-agent.
