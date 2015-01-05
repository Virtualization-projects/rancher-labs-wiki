If you're following along, you should have surmised that API resources are ultimately backed by database entities. The relationship between API resources and database tables is intentionally direct and simple. Tables map pretty closely 1:1 to API resources. 

The notable caveat to the above rule is that if Resource Bar extends from Resource Foo, then they will both use the foo database table.

## Creating a new database backed resource
This article: [Model: Create Database Type](https://github.com/rancherio/rancher/wiki/Model:-Create-Database-Type).

See if you can follow that article to create a new resource called ```Animal```. Add a field called ```species``` to it. Get it to the point where you can CRUD Animals (and the species field) in the API.

## Creating a resource that extends from and existing one
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

Need help? See the test [here](https://gist.github.com/cjellick/589aea867b67f0da4f6e).

