If you're following along, you should have surmised that API resources are ultimately backed by database entities. The relationship between API resources and database tables is intentionally direct and simple. Tables map pretty closely 1:1 to API resources. 

The notable caveat to the above rule is that if Resource Bar extends from Resource Foo, then they will both use the foo database table.

## Creating a new database backed resource
This article: [Model: Create Database Type](https://github.com/rancherio/rancher/wiki/Model:-Create-Database-Type).

See if you can follow that article to create a new resource called ```Animal```. Add a field called ```species``` to it. Get it to the point where you can CRUD Animals (and the species field) in the API.

## Creating a resource that extends from an existing one
Let's create a ```pet``` resource by extending ```animal```. We'll add the field ```owner``` to ```pet```.

It's pretty simple actually:

1. Open ```spring-core-model-context.xml``` ($CATTLE_HOME/cattle-iaas-model/src/main/resources/META-INF/cattle/core-model/spring-core-model-context.xml)
2. In the ```CoreTypeSet.typeNames``` property, follow the existing convention create a pet with parent of animal.
3. Apply the lessons learned in [3.1](https://github.com/rancherio/rancher/wiki/Cowpoke-3.1:-Modifying-an-existing-resource) to add the owner field and expose it in the API.

Get it working? Good. Able to create a Pet entity via the API UI? Great.

## TESTS!
Time to write some tests for our new resources. Actually, the time was probably as soon as we created the animal resource, but this is close enough. Way back in the first Cowpoke article, we setup the integration tests project in PyCharm. Open that back up.

> We largely rely on ***integration tests***, not unit tests. This is a philosophical approach to testing we've taken. We find integration tests to be more useful that mocked-out unit tests. We define an integration test as a test that executes against the Rancher API and thus requires, at minimum, a running Rancher API server. We also have ***validation tests*** which go a step beyond validation tests and might require multiple hosts be setup or specific production-like setups.

In PyCharm, create a new python file at: ```cattletest/core/test_animal.py```. Write a basic CRUD test. Take a look at ```test_account.py``` as an example. Test creating both an animal and a pet. In a single test function, you should be able to test creating an animal (and assert its species) and creating a pet (and assert its species and owner).

Need help on the test? See the test here: [test_animal_1.py](https://gist.github.com/cjellick/589aea867b67f0da4f6e#file-test_animal_1-py).

Let's extend that test to walk through the life cycle of a pet. Remove the ```create_animal()``` lines so that we can focus on just the pet. Then add this:
```
    animal = wait_success(admin_client, animal)

    assert animal.state == "active"

    animal = admin_client.wait_success(animal.deactivate())
    animal = admin_client.wait_success(animal.remove())
    admin_client.wait_success(animal.purge())
```
Run the test...

Uh-oh, the test failed with something like ```E AttributeError: 'dict' object has no attribute 'transitioning'```

That's because we haven't defined the life cycle the animal resource yet.

## Defining the life-cycle of a resource.
The life-cycle of a resource is made up a interconnected processes. Once you have defined a resource, you can attach processes to it. Processes are what drive the orchestration logic. You can list all the defined processes at:
* [http://localhost:8080/v1/processdefinitions](http://localhost:8080/v1/processdefinitions)
That lists all processes for all resources. You can get a resource-centric veiw at:
* [http://localhost:8080/v1/resourcedefinitions](http://localhost:8080/v1/resourcedefinitions)

Go there in the API UI and look for animal or pet. Nothing. There are no processes (and no states) associated to those resources. But go to [http://localhost:8080/v1/resourcedefinitions/1rd!instance](http://localhost:8080/v1/resourcedefinitions/1rd!instance) and click the resourceDot link. You'll see a diagram of all the processes attached to instances and how you can transition from one to the next. ***This is an incredibly powerful way to visualize everything that happens in the life cycle of an instance.***

While instance is a very important resource, it also has the most complicated life cycle. Checkout the [mount resource definition](http://localhost:8080/v1/resourcedefinitions/1rd!mount/resourcedot) for a much simpler life cycle. In fact, this is the "default" life-cycle. We can get it for animal (and by extension pet) with just a few lines of spring xml configuration:

1. In eclipse, open [spring-core-processes-context.xml](https://github.com/rancherio/cattle/blob/master/code/iaas/logic/src/main/resources/META-INF/cattle/core-process/spring-core-processes-context.xml)
2. Add ```<process:defaultProcesses resourceType="animal" />``` after the long list of similar defaultProcesses.
3. Save and restart the cattle debug process

Now you can go to [http://localhost:8080/v1/resourcedefinitions/1rd!animal/resourcedot](http://localhost:8080/v1/resourcedefinitions/1rd!animal/resourcedot) and see that we have a life-cycle for the animal resource.

Rerun the test!...
Darn, failed again, but this time with a message like 
```
>       assert animal.state == "active"
E       assert 'inactive' == 'active'
E         - inactive
E         ? --
E         + active
```
If you look at the resourceDot graph for animal, you'll see that it has the ```ActivateByDefault``` post create logic. What is ```ActivateByDefault```? It's a piece of logic that executes at a certain point in the resource's life cycle. In this case, it executes ***after*** the core create logic executes (hence **post**=ActivateByDefault as opposed to **pre**=ActivateByDefault). 

But more to the point, why didn't that logic activate the animal? Good news: ```ActivateByDefault``` is just a java class. Open it up in eclipse and take a look. If you look closely, that class looks for a config property specific to the resource to determine if it should activate it. Let's add that. 

1. In eclipse, open up [core-process/default.properties](https://github.com/rancherio/cattle/blob/master/code/iaas/logic/src/main/resources/META-INF/cattle/core-process/defaults.properties)
2. Add ```activate.by.default.animal=true```
3. Restart cattle.

Now, rerun that test and it should pass. YEEHAW!

### What just happened?
That was a lot to take in. What just happened? When we added that ```<process:defaultProcesses resourceType="animal" />``` line, we got a lot of stuff for free. Now would be a good time to ask questions. Ask in #rancher on freenode if you don't have more direct access to another Rancher engineer.

But in short, defaultProcesses is shorthand for a set of custom processes, states, and transition logic that a typical resource could be expected to have. But what are these processes, states, and transitions? Let's take a look at one simple process, instance start:
![](http://rancherio.github.io/rancher/instance-start.svg)

You can also see it here: [http://localhost:8080/v1/processdefinitions/1pd!instance.start/processdot](http://localhost:8080/v1/processdefinitions/1pd!instance.start/processdot)

Processes are defined as having start, transitioning, and done states. All processes are modeled in this fashion. For a process to be started, it obviously must be in the start state. In the above example, there are three valid start states for the process:

* stopped
* creating
* restoring

This means that you can only kickoff the InstanceStart process if the instance is in one of those states. Once the process has been started it will update the state to the transitioning state. For a process to be completed it must move from the transitioning state to the done state. If there is not logic attached to that transition, the orchestration system will simply update the state and be done.

Processes can have many start states, but only one done state. For InstanceStart, it is Running. This means that if an error occurs that prevents a resource from transitioning to done (for example, if an instance can't start), it will just hang and continue to retry. That said, the transitioning state can be defined as a start state for another process. So, an instance stuck in the transitioning state of ```starting``` can be moved to another state (specifically, the instance starting state is defined as a start state for instance.stop).

To make the orchestration system actually perform real operations, you must attach some logic. In the above diagram you can see that the ```InstanceStart``` logic has been attached to this process. One can attached any logic they choose. This makes the orchestartion system very flexible and extensible. At any point in the lifecycle of the resources you can plug-in any arbitrary logic. Again, ```InstanceStart``` is a java class. It does a lot of interesting things. Take a look.

Once you assign enough states and process to a resource you begin to construct a finite state machine for the resource. This is what you're viewing when you view the resourceDot graph of a resourceDefinition. 

**Next:** [[Add a new process handler to a resource.|Cowpoke 3.3: Add a new process handler to a resource]]
