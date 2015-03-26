# Overview

Configuration Framework in Cattle handles config updates for the agent's hosts and network-agent instance.

Each configuration is represented by **configItem** having its own logic for config file generation, and that item gets assigned to a particular host/network-agent. Bumping up the version for the config item would trigger ConfigUpdate event for the host/network-agent, and agent would process this event by:

* comparing the current version of the config with the requested one
* and if the requested version is higher, request the fresh config from the Cattle server
* cattle server will generate the fresh config upon the request, that agent will apply on the host, or - if the config 

More details on how to introduce new config item in cattle, is in the next section

# How to add a new config item to cattle