```
cow-poke
/'kou,pōk/, IPA: /kɑʊ poʊk/
noun
1. slang. A cowhand (one who tends free-range cattle)
```
[Source: wikitionary](http://en.wiktionary.org/wiki/cowpoke)

Get it? *Rancher* ... *Cattle* ...*Cowpoke*? 

This is a set of instructions and a tutorial designed to help new engineers get started on Rancher.io.
So saddle up pardner, let's get goin'. &lt;/bad cowboy puns&gt;

***DISCLAIMER:*** This setup guide targets full-stack Rancher engineers/hackers that may need to work on the cattle orchestration server (java) or one of the agents/microservices that interact with it (python, go, node). If you don't fit that profile, consider following [these much simpler instructions](https://github.com/rancherio/rancher#management-server) to get Rancher up and running quickly.

***DISCLAIMER #2:*** This is a very opinionated setup guide. It prescribes certain IDEs and methodologies. The motivation for being so opinionated is two-fold: 1. To offer a level playing field to everyone working on Rancher. 2. To teach-by-example. So that if you have a different way of doing things (ie, prefer a different IDE or operating sytem) you can at least reference this document to find out how things are expected to work.

Overview
--------
Rancher.io is an open source project that provides infrastructure...actually, [just read this](https://github.com/rancherio/rancher).

We'll assume you have an idea of what Rancher.io is and what it does and just focus on getting your environment up and running. In this guide, we'll do the following: 
 1. Setup the necessary tech on your development machine
 2. Clone the Rancher source code repo and configure its projects in our IDEs
 4. Launch Rancher and play around with it a bit
 5. Run integration tests and verify everything is working

Details
-------
### Pre-reqs
Assuming that you're woking on a Mac, install Xcode from the app store (some pieces of software require it). Open it after it installs so that you can accept the Terms. 

### Setup necessary tech
Here's the list of the core libraries/technologies/software that you'll need:
* docker [Mac](http://docs.docker.com/mac/step_one/)/[Linux](http://docs.docker.com/linux/step_one/)
* mysql server and client
* JDK 7
* Python 2.7 and some python tools: pip, virtualenv, tox
* Homebrew coreutils for things like greadlink

If you're on a Mac and use Homebrew, you can follow these steps:
```bash
touch ~/.profile # If you don't have it
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew doctor
brew tap caskroom/homebrew-cask
brew install brew-cask
brew cask install virtualbox
brew install coreutils
brew install maven
brew install liquibase
echo "export LIQUIBASE_HOME=$(find /usr/local/Cellar/liquibase -name libexec)" >> ~/.profile
brew install gnu-sed --with-default-names
brew install boot2docker
boot2docker init
boot2docker up
$(boot2docker shellinit)
echo '$(boot2docker shellinit)' >> ~/.profile # Get docker going in all new shells
docker run busybox # Prove it works!
brew install mysql
mysql.server start
mysql -uroot # Prove it works!
sudo easy_install pip
sudo pip install tox # This gets your virtualenv too
```   
Install JDK 7 manually since the Homebrew install seems flaky. Just get the latest [from Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Make your JAVA_HOME point to 1.7:
```
echo "export JAVA_HOME=$(/usr/libexec/java_home -v 1.7)" >> ~/.profile
```
### Clone repos and setup projects
Clone cattle to your workspace directory:
```bash
cd <your workspace directory>
git clone https://github.com/rancherio/cattle.git
```
That's it. All the code is in the cattle repo. [The rancher project](https://github.com/rancherio/rancher) actually just packages up the code in the cattle repo.

> **Note:** From now on, the path to where you cloned cattle will be referred to as ***$CATTLE_ROOT***. Go ahead and export that environment variable if you just want to copy and paste commands:
```bash
  export CATTLE_ROOT=<your working directory>
```

Before we move on to setting up the management server in Eclipse, let's initialize our database server with the proper database and users:
```
cd $CATTLE_ROOT
mysql -uroot < resources/content/db/mysql/create_db_and_user_dev.sql
```

### Install and Configure Eclipse
> ***Note:*** If you aren't working on the cattle orchestration server which is written in java, you don't need to install, configure, and run in Eclipse. Instead, you can just run the rancher server as a container. Instructions are [in the Rancher repo README.md](https://github.com/rancherio/rancher#management-server)

For Java, we'll assume the latest version of ***[Eclipse](https://www.eclipse.org/downloads/packages/)*** (flavor: *Eclipse IDE for Java Developers*). 

> **Note:** We'll assume PyCharm for the python components. For golang, vim + vim-go is the best we've found. For Node.js and the rest of the front end, we're using vim as well.

Install eclipse now and then do the following:

1. In Eclipse, open the marketplace and install these plugins:
 * Spring IDE. It has a ton of optional modules. You only need the "core" module; deselect the rest.
 * M2E (maven2eclipse). It should already be installed, but make sure it is.
1. Make the "Plugin execution not covered by lifecycle..." error go away:
  * Preferences > Maven > Errors/Warnings > Plugin execution not covered...: Ignore

### Import and setup cattle projects in Eclipse
Now we can move on to configuring the project in Eclipse.
But first, copy the Cattle Eclipse launch config to a different location:  
```  
cp $CATTLE_ROOT/tools/eclipse/Cattle.launch $CATTLE_ROOT/code/packaging/app/
```
That’s so that in Eclipse we can eventually close the top level cattle project. We need to do that to avoid duplicate code issues in Eclipse. 

**TODO** *Move Cattle.launch to the app directory and rename Cattle.launch.example and then update this documentation*

The cattle repo contains a lot of sub-projects. All the java sub-projects are maven based, so it's easy to get them all setup in eclipse:

1. File > Import... > Maven > Existing Maven Projects
1. Root Directory: $CATTLE_ROOT
1. Leave all the projects selected.
1. ...Wait for Maven to build...
1. Close the top-level "cattle" project so that you don't get any duplicate code issues.
 
> Much projects. Such Maven. Wow.

### Launch Rancher!
Remember that Cattle.launch we copied? That's how we launch the management server:
 1. Click the Run Dropdown
 2. Debug Configurations...
 3. Java Applications: Cattle (click it)
 4. The config is already there, we just need to change some arguments...Click the Arguments tab
 5. To configure Rancher for mysql, add something like the following to ***VM arguments*** (depending on your setup)(***DO NOT PUT IT IN THE PROGRAM ARGUMENTS***):

  ```
  -Ddb.cattle.database=mysql
  -Ddb.cattle.mysql.port=3306
  -Dtask.config.item.source.version.sync.schedule=5
  -Dtask.config.item.migration.schedule=5
  -Didempotent.checks=false
  ```
 6. Apply. Debug. Rancher is running.
 7. Go to [http://localhost:8080](http://localhost:8080) to prove it.
 8. We need to add a compute node so that rancher has a place to drop containers. Go to [http://localhost:8080](http://localhost:8080). Click on "Add Host" and get the command from the UI under the Custom/Bare Metal option. 

That's taken from the [rancher repo's documentation](https://github.com/rancherio/rancher). We skipped the step of starting the management server container because we're running that out of eclipse. The IP address is the default gateway that is setup by virtualbox.

### Run integration tests and verify everything is working
We'll run the python integration tests out of PyCharm. Go install the latest [PyCharm Community Edition](https://www.jetbrains.com/pycharm/download/) and open it up.

Now, create a project for the integration tests via: File > Open > $CATTLE_ROOT/tests/integration

Set the python interpreter as a virtual environment:
> **Note:** You can setup the .venv from the command line if you prefer. PyCharm will automatically pick it up.

1. PyCharm > Preferences > Project Interpreter
1. Click the gear box next to the Project Interpreter drop down on the right
1. Create virtualenv. 
 * Name: .venv
 * Location: $CATTLE_ROOT/tests/integration/.venv
 * Base Interpreter: Choose the Default OS X Python.

Create your project with the virtual environment.

After your project is created, setup Python integrated tools:
1. PyCharm > Preferences > Tools > Python Integrated Tools
1. Package requirements file...click ```...``` and select $CATTLE_ROOT/tests/integration/requirements.txt
1. Default test runner: py.test
1. Click Okay

> **Note:** Another way to set up and activate the requirements file, open the terminal and run these commands.
```
cd $CATTLE_ROOT/tests/integration
. .venv/bin/activate
pip install -r requirements.txt
```

We're ready to run the integration tests. There's a run configuration drop down in the tool bar. It's just an empty arrow now.
 1. Click the Run dropdown. Click 'Edit Configurations...'
 1. + > Python tests > py.test
  * Name: Integration-tests
  * Target: $CATTLE_ROOT/tests/integration/cattletest
 1. Run it! It's in the drop down now. Select it and hit the green arrow.

> **Note:** If you didn't activate the requirements file through the commands, the tests may not run immediately. If you get an error, open a specific test case by expanding cattletest > core and click on a test case. Try to run the test case and PyCharm should pop up a note to install requirements. Install the requirements and try to run the tests again. 

Hopefully, you got the green bar.

You could also run the test suite via tox:
```
cd $CATTLE_ROOT/tests/integration
tox
```

Conclusion
----------
YEE-HAW PARDNER! You're an official cowpoke. Now go russle up some bugs and corral 'em.

Further Reading
---------------
[[Cowpoke 2: Halp! (Debugging, troubleshooting, starting over)]]

[[Cowpoke-3.0:-Tutorial-introduction-and-the-Rancher-API]]