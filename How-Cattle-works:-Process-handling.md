## Overview

This article described how process works in cattle.

## Process

Most operation of the resources are defined in resource lifecycle. _spring-core-process-contexts.xml_ define the lifecycle for each resource. Most of resources just follow the _defaultProcess_, for example [this process map](https://github.com/rancherio/rancher/wiki/How-Cattle-works:-Process-handling) shows the _defaultProcess_ applied on *host* resource.

The process different from _defaultProcess_ can be defined in the file as well. _defaultProcess_ is not required for each resource. For example the following code defined lifecycle of _snapshot_:

>     <!-- Snapshot -->
>     <process:process name="snapshot.create" resourceType="snapshot" start="requested" transitioning="creating" done="created" />
>     <process:process name="snapshot.backup" resourceType="snapshot" start="created" transitioning="backing-up" done="backed-up" />
>     <process:process name="snapshot.remove" resourceType="snapshot" start="creating,created,backing-up,backed-up" transitioning="removing" done="removed" />

Defined the process for _snapshot_, which doesn't involve defaultProcess at all.

## Process handler

The process name defined in "spring-core-process-contexts.xml" can be used to identify the action would be taken when resource state changed. All the process handling logic is in _DefaultProcessInstanceImpl.java_. There are three places can be hooked in to process handling, which called: _Prelistener_, _Handler_ and _Postlistener_ respectively.

Most handlers should expands AbstractObjectProcessHandler. The AbstractDefaultProcessHandler adds two things on top of the AbstractObjectProcessHandler for convenient:

1. Sets priority to Priority.DEFAULT. if you don't set a priority it is assumed to be Priority.SPECIFIC which runs before DEFAULT

2. Automatically turns the classname in the the processname to attach to.  So the class InstanceStart becomes "instance.start" which set process name which should match the action name. Mostly the Java class name should match the "ResourceAction.java" pattern, then process name can be set automatically.

The core function of handler class is:

>     public HandlerResult handle(ProcessState state, ProcessInstance process)

The state field contained resource object(getResource()).

+ Return: _HandlerResult_ Parameters: 
+ _shouldContinue()_: If it's necessary to continue executing following handlers.  
+ _Key value pair_: The key value pair would be used to update resource. It would be represented by a nested map without the top level key(resource type itself). All the children resources can be updated as well. For example, assume InstanceHostMap has: Instance, Host, Data fields, and instance has field name, the map format would be:
{ Instance: { name: "xxx" ...}, Host: { ... }, Data: { ... } }
+ _withChainProcessName()_: This option would invoke a following action immediately, thus skip the unfavorable intermediate state, prevent it from seen by user. For example, this option is used by _instance.stop_ to skip the *Stopped* state when restart is required. It would invoke _instance.start_ immediately, without leaving room for *Stopped* state.

## AgentBasedProcessHandler

AgentBasedProcessHandler is the basic class for all communication with agent. The logic of generating correlated handler is at _spring-core-logic-context.xml_

Here is a example of definition of a bean:

>     <bean class="io.cattle.platform.process.common.handler.AgentBasedProcessHandler"
>         p:reportProgress="true"
>         p:name="ImageStoragePoolMapActivate"
>         p:commandName="storage.image.activate"
>         p:agentResourceRelationship="storagePool"
>         p:dataTypeClass="io.cattle.platform.core.model.ImageStoragePoolMap"
>         p:processNames="imagestoragepoolmap.activate" >
>         <property name="priority">
>             <util:constant static-field="io.cattle.platform.util.type.Priority.DEFAULT"/>
>         </property>
>     </bean>

+ _name_: Name of class. Matched naming convention of object ImageStoragePoolMap.activate.
+ _commandName_: The event's command name. Define the which kind of event is transferring to agent.
+ _dataTypeClass_: The resource type would be attached to the event to the agent.
+ _agentResourceRelationship_: Identify the agent this command would be send to. The class here would be a child of main resource, and would implement the method _getAgentId()_ to identify the agent.
+ _processNames_: The process name used to identify this class, which would be called during handling this process.


The main function of _AgentBasedProcessHandler_ is still handle(). There are three resources define how it works:
+ _eventResource_: The resource would be transferred in event as main resource.
+ _dataResource_: The other data resource would be come along with the event.
+ _agentResource_: The agent resource this event would be sent to.

The data come along with the event is the serialized form of _dataResource_. The serializer is defined at _defaults.properities_, like this:

> event.data.imageStoragePoolMap=storagePool|image
> event.data.instance=volumes[${event.data.volume}]|offering|image|ports|nics[${event.data.nic}]|instanceLinks[targetInstance]  
> event.data.instanceHostMap=instance[${event.data.instance}]|host
> event.data.nic=ipAddresses[subnet]|network[networkServiceProviders|networkServices]
> event.data.volume=offering|instance|storagePools|image  
> event.data.volumeStoragePoolMap=volume[${event.data.volume}]|storagePool

The serialized form would contained all the informations agent needs to know to perform the correct action.

The reply would be in the form of Event as well, whose data would be used as a parameter of returned HandlerResult and update the resource immediately.