## Overview

This article described how process works in cattle.

## Process

Most operation of the resources are defined in resource lifecycle. The file "spring-core-process-contexts.xml" define the lifecycle for each resource. Most of resources just follow the "defaultProcess", for example http://localhost:8080/v1/resourcedefinitions/1rd!host/resourcedot shows the defaultProcess applied on *host* resource.

The process different from defaultProcess can be defined in the file as well. defaultProcess is not required for each resource. For example:

>     <!-- Snapshot -->
>     <process:process name="snapshot.create" resourceType="snapshot" start="requested" transitioning="creating" done="created" />
>     <process:process name="snapshot.backup" resourceType="snapshot" start="created" transitioning="backing-up" done="backed-up" />
>     <process:process name="snapshot.remove" resourceType="snapshot" start="creating,created,backing-up,backed-up" transitioning="removing" done="removed" />

Defined the process for snapshot, which doesn't involve defaultProcess at all.

## Process handler

The process name defined in "spring-core-process-contexts.xml" can be used to identify the action would be taken when resource state changed. All the process handling logic is in "DefaultProcessInstanceImpl.java". There are three places can be hooked in to process handling, which called: Prelistener, Handler and Postlistener respectively.

The handler would mostly expands AbstractDefaultProcessHandler, which set process name which should match the action name. Mostly the Java class name should match the "ResourceAction.java" pattern, then process name can be set automatically.

The core function of handler class is:

>     public HandlerResult handle(ProcessState state, ProcessInstance process)

The state field contained resource object(getResource())

Return: Handler:
Parameter: shouldContinue: If it's necessary to continue executing following containers.  
Key value pair: The key value pair would be used to update resource. It would be represented by a nested map without the top level key(resource type itself). All the children resources can be updated as well. For example, assume InstanceHostMap has: Instance, Host, Data fields, and instance has field name, the map format would be:
{ Instance: { name: "xxx" ...}, Host: { ... }, Data: { ... } }

## AgentBasedProcessHandler

AgentBasedProcessHandler is the basic class for all communication with agent.