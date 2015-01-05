Now that we've defined a new resource and created a set of processes for it (the default processes), we can attach logic to it.

In the cattle-iaas-logic project, create a new class: 

* ```io.cattle.platform.process.animal.AnimalPostActivate``` 
  * That extends 
    * ```io.cattle.platform.process.common.handler.AbstractObjectProcessLogic``` 
  * And implements 
    * ```io.cattle.platform.engine.handler.ProcessPostListener```
    * ```io.cattle.platform.util.type.Priority```
  * And is annotated with
    * ```javax.inject.Named```


Here's the easy/boring method implementation you'll need
```
@Override
public String[] getProcessNames() {
  return new String[] { "animal.create" };
}

@Override
public int getPriority() {
  return Priority.DEFAULT;
}
```

The interesting logic takes place in this method:
```
public HandlerResult handle(ProcessState state, ProcessInstance process) {
  # Your logic here
  return null;
}
```

Here's a simple sample to base your handler off [io.cattle.platform.process.instance.InstanceStopPostAction](https://github.com/rancherio/cattle/blob/e8f6206a13a4a8590ec0d9e2a71ac17ade896b04/code/iaas/logic/src/main/java/io/cattle/platform/process/instance/InstanceStopPostAction.java)

If you get that wired up correctly, you should be able to a couple of things:

* If you go to [http://localhost:8080/v1/resourcedefinitions/1rd!animal/resourcedot](http://localhost:8080/v1/resourcedefinitions/1rd!animal/resourcedot), you should see *post=AnimalPostActivate* logic added to animal.activate process