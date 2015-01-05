Now that we've defined a new resource and created a set of processes for it (the default processes), we can attach logic to it.

## Adding a post-activate handler
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
Here's a simple example that you could base your handler off of:[io.cattle.platform.process.instance.InstanceStopPostAction](https://github.com/rancherio/cattle/blob/e8f6206a13a4a8590ec0d9e2a71ac17ade896b04/code/iaas/logic/src/main/java/io/cattle/platform/process/instance/InstanceStopPostAction.java)

If you get that wired up correctly, you should be able to a couple of things:

* If you go to [http://localhost:8080/v1/resourcedefinitions/1rd!animal/resourcedot](http://localhost:8080/v1/resourcedefinitions/1rd!animal/resourcedot), you should see *post=AnimalPostActivate* logic added to animal.activate process
* If you add a breakpoint to the handle method, you'll hit it when you create a resource. Add the breakpoint and rerun the integration test to prove it.

## Adding and testing some logic
Let's add some logic to record the number of legs our animal has when it is activated.

In the spirit of TDD, let's first write a failing test. Add this to your test:
```
assert animal.data['numberOfLegs'] == "4"
```
after you assert that it's active.

Run the test and watch it fail. Then, in the handler you've written, add the following logic:
```
Animal animal = (Animal)state.getResource();
DataAccessor.fromDataFieldOf(animal).withKey("numberOfLegs").set("4");
ObjectManager objectManager = getObjectManager();
objectManager.persist(animal);
return null;
```

Hopefully, it should be obvious that you'll need to import the relevant classes for that to work. Once the java class is compiling, rerun the python test and it should pass.

Did it work? Awesome, you just created a post-activate handler that modifies a resource.

**Next:** [[Create an external event handler for a resource|Cowpoke 3.4: Create an external event handler for a resource]]
