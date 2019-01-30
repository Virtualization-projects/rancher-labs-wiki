# Setting up for Rancher development
This page will go over setting up your environment for development work on the current version of rancher. This is primarily built on the premise of setting up in a MacOS environment, but most of it should carry over to linux as well.

## Install Dependencies
- ### Go
  Rancher is built in Go, which can be downloaded here: https://golang.org/dl/. Alternatively, on MacOS it can be installed with [homebrew](https://brew.sh/) using the following command: `brew install go`

  additionally, you should add the gopath bin directory to your path. Add the following to your .bashrc or other shell file
  ```
  export PATH=${GOPATH//://bin:}/bin:$PATH
  ```
- ### Kubernetes
  The local Rancher instance will be hosted in a local installation of kubernetes. Kubernetes can be downloaded from here: https://kubernetes.io/docs/setup/pick-right-solution/#local-machine-solutions
  - Docker for Mac
    On MacOS, the easiest way to set up kubernetes is through Docker for Mac, available here: https://docs.docker.com/install/#supported-platforms. Once Docker is installed, open the settings, switch to the kubernetes tab, and make sure kubernetes is enabled (and that actual kubernetes is selected, rather than swarm).

![](https://i.imgur.com/RfmuSj7.png)
- ### IDE
  Most of us at Rancher labs use GoLand, and the rest of this document will be written assuming you are as well, but there are several options
  - [GoLand](https://www.jetbrains.com/go/)
  - [vim + vim-go](https://github.com/fatih/vim-go)
  - [VS Code + vscode-go](https://github.com/Microsoft/vscode-go)
  - [Atom + go-plus](https://atom.io/packages/go-plus)
- ### Trash
  Trash is a dependency management tool written by Rancher for use with rancher. You'll only need this if you're making changes to other rancher packages that rancher/rancher lists as dependencies. It is available for download here: https://github.com/rancher/trash/releases. Note that sometimes, you may have to run it twice, if for example, you get lots of unexpected changes after the first pass.
- ### ngrok
  ngrok allows you to forward an external url to your local webservers. It is available here: https://ngrok.com. On MacOS it can be installed via homebrew with the command `brew cask install ngrok`

## Clone repo and set up project structure
There are many ways you can set up your gopath and folder structure, but the cleanest way for rancher is as follows (also included are the [rancher/types](https://github.com/rancher/types) project and [rancher/norman](https://github.com/rancher/norman) projects, for example purposes)
```
RANCHER_DEV_ROOT_DIR
  - rancher
    - src
      - github.com
        - rancher
          - rancher (actual repo)
  - types
    - src
      - github.com
        - rancher
          - types (actual repo)
  - norman
    - src
      - github.com
        - rancher
          - norman (actual repo)
```
This assumes all of your rancher projects are stored in one single parent rancher directory (referenced as RANCHER_DEV_ROOT_DIR, but the name is unimportant). Notice that these directories (below the src directories) mirror the structure within rancher/rancher's vendor directory
To start, you will likely need at minimum [rancher/rancher](https://github.com/rancher/rancher) and [rancher/types](https://github.com/rancher/types) cloned.

- ### Setting up remotes
In order to avoid accidentally creating a branch in the rancher/rancher repo please set your remote accordingly with your fork. 
```
origin    git@github.com:AwesomeContributor/rancher.git (fetch)
origin    git@github.com:AwesomeContributor/rancher.git (push)
upstream    https://github.com/rancher/rancher.git (fetch)
upstream    https://github.com/rancher/rancher.git (push)
```
HTTPS urls allow for unauthencitcated cloning and pulling. Futhermore, you have 2-factor auth set up (**right?**) and https pushing will fail. 
To get the most up-to-date version run:

 `git fetch upstream`

## Set up GoLand
As discussed above, we primarily use GoLand for development. There are a few things to do here

### Set up project GOPATH
The GOPATH for a given project should be one step up from the `src` folder

### Set up the build target
  Attach the build go main.go in the root of the project
  - add environment variable `KUBECONFIG` set to `{homedirectory}/.kube/config` (on mac `users/maxkorp/.kube/config`)
  - change go tool arguments to `-i -gcflags="-N -l"`
  - change program arguments to `--add-local=true`

![](https://i.imgur.com/1lzSadD.png)


## Run!
Start the build in GoLand. The service will be available at http://localhost:8080. Ignore the scary cert warning (or use ngrok as a way around.

### Changing the password
You have the option here to change the password (from the default of `admin`). If you are running rancher on AWS or any other external hosting, or it in any way is available to the outside world, you _must_ change this, or you will have an _extremely_ bad and expensive time, with many large unwanted nodes and workloads spun up in short order. Note, however, that the tests expect the default username/password combo of `admin`/`admin`, and so if you are running in a way that the instance is not available to the outside world and does not have any production keys, you can simply re-enter the password as `admin`

### Preventing exposure of API Keys and other sensitive information
[git-secrets](https://github.com/awslabs/git-secrets#git-secrets) is useful for preventing accidental exposure of sensitive information when committing code. On MacOS run 
``` brew install git-secrets ```
Then add hooks to the repo
```
cd /path/to/my/repo
git secrets --install
git secrets --register-aws
```
### ngrok
If you are running through ngrok, make sure to go to the settings tab and change the `server-url` field to match your ngrok https forwarding address

## Working in the `rancher/types` repo
In both your terminal and in goland, you'll want to set your GOPATH env var to one step above the src folder, as you did with the `rancher/rancher` repo above

To get started, run `make`.

After making any code changes, run `go generate`, and then rerun the linking process described below.

### Linking the project
  In your `rancher/rancher` dir (`RANCHER_DEV_ROOT_DIR/rancher/src/github.com/rancher/rancher`), remove everything under the `vendor/github.com/rancher/types` directory (but not the directory itself), and symlink everything within `RANCHER_DEV_ROOT_DIR/types/src/github.com/rancher/types` in. There is a script in the section below to help with this.

#### Note: This linking process is the same for other subprojects, e.g. `rancher/norman`

Once changes to `rancher/types` have landed, you must edit the sha in the `vendor.conf` file in the root of `rancher/rancher`, and then run `trash`. Remember, as mentioned above, you may need to run trash more than once, if you see changes in the `vendor` directory for other modules you did not change.

# Setting up and running the Tests

## Install Dependencies
- ### Install helm
  - Clone https://github.com/rancher/helm into go/src/k8s.io
  - Change to rancher branch
  - Run `make bootstrap build`
  - Copy bin/tiller & bin/helm to /urs/local/bin and chmod u+x
- ### Install docker-machine
  - Clone https://github.com/rancher/machine into go/src/github.com/docker
  - In cmd/docker-machine run `go build`
  - Copy docker-machine to /urs/local/bin
- ### Install Python 3.7
  On mac it can also be installed via homebrew. See [here](https://docs.python-guide.org/starting/install3/osx/). Just running `brew install python` should get you there.
- ### Upgrade pip
  ```sh
  sudo pip3 install --upgrade pip
  ```
- ### install virtualenv

  ```sh
  sudo pip3 install virtualenv
  ```
- ### create a virualenv directory
  You can place this wherever you choose, but the rest of the docs and the scripts below will assume it is placed at `$HOME/.venv/rancher`
  ```sh
  mkdir -p ~/.venv/rancher
  virtualenv -p python3 ~/.venv/rancher
  ```
- ### Activate the virtual environment
  ```sh
  source ~/.venv/rancher/bin/activate
  ```
  To deactivate, run `deactivate`. There is a hook below in the scripts section to handle this for you automatically, assuming you followed the setup outlined in this document
- ### Install tox
  Rancher uses [tox](https://pypi.org/project/tox/) for running tests
  ```sh
  pip install tox
  ```
- ### Run the tests
  ```python
  tox
  ```

# Helpful Scripts
Assuming the directory structure outlined above, these scripts help to reduce some of the tedium in keeping your environment set up locally.
They assume you have the following environment variables set
- `RANCHER_DEV_ROOT_DIR`: as describe above, the folder in which all of your other rancher projects are contained
- `RANCHER_VENV_DIR`: the directory you chose for your virtual environment, e.g. `$HOME/.venv/rancher`

### go
```sh
function cowhand_go {
  cd "$RANCHER_DEV_ROOT_DIR/$1/src/github.com/rancher/$1"
}
```
Since the directory structure is a bit terse, this is just a shortcut to a long cd path
e.g. `cowhand_go rancher` will take you to `RANCHER_DEV_ROOT_DIR/rancher/src/github.com/rancher/rancher`

### github
```sh
function cowhand_github {
  open https://github.com/rancher/$1
}
```
This only works on mac, but it will open your browser to the appropriate repo.
e.g. `cowhand_github types` will open your browser to `https://github.com/rancher/types`

### activate
```sh
source $RANCHER_VENV_DIR/bin/activate
```
this activates your rancher virtual environment

### deactivate
```sh
function cowhand_deactivate {
  deactivate
}
```
this deactivates your rancher virtual environment

### link
```sh
function cowhand_link {
  if [ ! -d vendor ]; then
    echo Run in project root where vendor is
    return 1
  fi
  for i
  do
    if echo $i | grep /; then
        echo "names cant have / in them"
        return 1
    fi

    if [ -d vendor/github.com/rancher/$i ]; then
        rm -rf vendor/github.com/rancher/$i
    elif [ -e vendor/github.com/rancher/$i ]; then
        rm vendor/github.com/rancher/$i
    fi
    mkdir -p vendor/github.com/rancher/$i
    (
        cd vendor/github.com/rancher/$i
        ln -s ./../../../../../../../../../${i}/src/github.com/rancher/${i}/[^v]* .
    )
  done
}
```
This links rancher subprojects into the current rancher project.
e.g. if you are in your `rancher/rancher` directory, to link in local changes from your `rancher/types` directory, you would run `cowhand_link types`.

Note that this will generate a large git diff, as an entire subtree has been modified.

### unlink
```sh
function cowhand_unlink {
  if [ ! -d vendor ]; then
    echo Run in project root where vendor is
    return 1
  fi

  for i
  do
    git checkout -f "vendor/github.com/rancher/$i"
    git clean -xdff "vendor/github.com/rancher/$i"
  done
}
```
The opposite of link, to go back to a normal state. For example to unlink local changes `rancher/types` from within your `rancher/rancher` directory, run `cowhand_unlink types`.

### clone
```sh
function cowhand_clone {
  newDir=$RANCHER_DEV_ROOT_DIR/$1/src/github.com/rancher/$1
  mkdir -p $newDir
  git clone "git@github.com:rancher/$1" $newDir
}
```
Just a shorthand way of cloning rancher repos into the prescribed folder structure


### Tying it all together
If you'd like to consolidate these all to one command, here is the function to do so
```sh
function cowhand {
  toExec=$1
  shift
  case "$toExec" in
    "go" | "g")
      cowhand_go $@
      ;;
    "github" | "gh")
      cowhand_github $@
      ;;
    "activate" | "a")
      cowhand_activate $@
      ;;
    "deactivate" | "d")
      cowhand_deactivate $@
      ;;
    "link" | "l")
      cowhand_link $@
      ;;
    "unlink" | "u")
      cowhand_unlink $@
      ;;
    "env" | "e")
      cowhand_env_hook $@
      ;;
    "clone" | "c")
      cowhand_clone $@
      ;;
    *)
      echo "You have failed to specify what to do correctly."
      ;;
  esac
}
```
e.g. to run `cowhand_link types` run `cowhand l types`.

### CD Hook

To tie into your shell, you can use this cd hook.
This looks for a .gopath file up the tree from a given directory, and if it has contents uses those as gopath, or if it is empty, just sets it's containing dir as gopath. Similarly, if it finds a .venv file, it will use the env specified within.
```

findGopath () {
  cdir=$PWD
  while [ "$cdir" != "/" ]; do
    if [ -e "$cdir/.gopath" ]; then
      if [ "$cdir" == "$HOME" ]; then
        _locality="global"
      else
        _locality="local"
      fi

      _gopath=$(cat $cdir/.gopath)
      if [ -n "$_gopath" ]; then
        _type="explicitly set"
      else
        _type="inferred"
        _gopath=$cdir
      fi

      if [ "$_locality" == "global" ] && [ "$_type" == "inferred" ]; then
        _gopath=""
      fi

      _found="1"

      break
    fi
    cdir=$(dirname "$cdir")
  done

  if [ -z "$_found" ] && [ -e "$HOME/.gopath" ]; then
    _gopath=$(cat $HOME/.gopath)
    _locality="global"
    if [ -n "$_gopath" ]; then
      _type="explicitly set"
    else
      _type="inferred"
      _gopath=$HOME
    fi

    if [ "$_locality" == "global" ] && [ "$_type" == "inferred" ]; then
        _gopath=""
      fi

      _found="1"
  fi
  if [ "$_gopath" != "$GOPATH" ]; then
    if [ -n "_gopath" ]; then
      export GOPATH=$_gopath
      echo "Found $_locality .gopath, exporting $_type $_gopath"
    else
      echo "no .gopath found, unsetting GOPATH"
      unset GOPATH
    fi
  fi
  unset cdir
  unset _found
  unset _gopath
  unset _type
  unset _locality
}

findVenv () {
  cdir=$PWD
  while [ "$cdir" != "/" ]; do
    if [ "$cdir" != "$HOME" ] && [ -e "$cdir/.venv" ]; then
      _venv=$(cat "$cdir/.venv")
      if [ -n "$_venv" ]; then
        _found="1"
        if [ -n "$VIRTUAL_ENV" ]; then
          _current=$(basename $VIRTUAL_ENV)
        fi

        if [ "$_current" != "$_venv" ]; then
          source "$HOME/.venv/$_venv/bin/activate"
          echo ".venv file found, activating environment \"$_venv\""
        fi
      fi
      break
    fi

    cdir=$(dirname "$cdir")
  done

  if [ -z "$_found" ] && [ "$(type -w deactivate)" == "deactivate: function" ]; then
    echo "no .venv found, deactivating"
    deactivate
  fi
  unset cdir
  unset _found
  unset _venv
  unset _current
}


cd () {
  builtin cd "$@"
  findGopath
  findVenv
}
```

# Gotchas
`panic: creating CRD store customresourcedefinitions.apiextensions.k8s.io is forbidden: User "system:anonymous" cannot list customresourcedefinitions.apiextensions.k8s.io at the cluster scope`
Check your kubectl context