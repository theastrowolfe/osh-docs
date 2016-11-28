Developer's Guide
===

This guide is meant to help you setup a development environment based on the Eclipse IDE so that you can extend OpenSensorHub (OSH for short) with your own sensor drivers, web services and other components.

Don't forget to send us a Pull Request if you want to contribute your work back to this project. Other users may be interested by your modules and bug fixes! 

Of course, contributing new modules to the community is optional as our license does not prevent proprietary and commercial derived work. However, keep in mind that **if you modify the source files we provide, you must make it available publicly in source form**. 

This page provides instructions for three possible options, depending on your level of involvement:

  * Exploring the code online

  * Building from source using [Gradle](#building-with-gradle) or [Eclipse](#building-with-eclipse)

  * Contributing software and fixes to the project



### Exploring the Code

If you just want to explore the code, you can browse the source online directly on [Github](https://github.com/opensensorhub). Alternatively, you can download it to your computer using the *Download ZIP* link on each GitHub repository or using the `git` program (please see the next section if you want to do just that).

To start with, the repositories of interest are OpenSensorHub [Core](https://github.com/opensensorhub/osh-core) and [Sensor Drivers](https://github.com/opensensorhub/osh-sensors).


### Getting the Code

Git (we use `git 2.7`) is used to download the code from the Github repositories.

For instance, you can download the code for the core modules using the following command:

```bash
$ git clone --recursive https://github.com/opensensorhub/osh-core.git
```

There are many different repositories that contain various modules for OpenSensorHub. You can browse them all on [Github](https://github.com/opensensorhub) so you can decide which ones are of interest for you but here is a summary of the most important ones:

  * Core Modules: <https://github.com/opensensorhub/osh-core> 
  * Comm Protocols: <https://github.com/opensensorhub/osh-comm>
  * Sensor Drivers: <https://github.com/opensensorhub/osh-sensors> 
  * Processing Modules: <https://github.com/opensensorhub/osh-processing>
  * Other Services: <https://github.com/opensensorhub/osh-services>
  * Storage Backends: <https://github.com/opensensorhub/osh-persistence>
  * Security Stuff: <https://github.com/opensensorhub/osh-security>
  * Android: <https://github.com/opensensorhub/osh-android>


### Building with Gradle

The following instructions are for building using the Gradle Wrapper command `gradlew`. This small executable is included in the repository and will automatically fetch the correct version of Gradle for you. If you want to use your own version of Gradle, use the `gradle` command instead, but please note that our build scripts need version 3.1 or later to get support for composite builds. If you want to install Gradle yourself we recommend downloading it directly from the [Gradle Website](https://gradle.org/gradle-download/) as packages provided in Linux distributions may only include earlier versions.

#### Build the core

Use the following command to clone the [osh-core](https://github.com/opensensorhub/osh-core) repository:

```bash
$ git clone --recursive https://github.com/opensensorhub/osh-core
```

You can then build the code using the gradle wrapper command `gradlew` (`gradlew.bat` on Windows):

```bash
$ cd osh-core
$ ./gradlew build
```

This command will build a JAR file for each module in the corresponding subfolder `{module-name}/build/libs` as well as a ZIP file containing all built module JARs in `build/distributions`.

You can optionally install the generated artifacts to your local Maven repository with:

```bash
$ ./gradlew install
```

_Note 1: The first time you launch Gradle, the build process can take a while because Gradle fetches its own dependencies (i.e. Gradle plugins) as well as OpenSensorHub's dependencies. Later builds will go faster because these dependencies are cached locally._

_Note 2: Some of the JUnit tests automatically run during the 'test' phase of the OSH build process need to instantiate a server on port 8888. These tests will fail if something else is running on this port._

#### Build sensor modules

Sensor drivers are provided in various [osh-sensors-***](https://github.com/opensensorhub?q=osh-sensors) repositories. First clone one of these repos in the same folder as osh-core (i.e. `osh-core` and `osh-sensors` must be sibbling directories):

```bash
$ git clone https://github.com/opensensorhub/osh-sensors
```

Some sensor drivers need specific communication modules to work (e.g. RXTX serial comm or Bluetooth LE). If you're building a dev version, you'll also have to build these modules from source code. For this, clone the [osh-comm](https://github.com/opensensorhub/osh-comm) repository at the same level as the other repos:

```bash
$ git clone https://github.com/opensensorhub/osh-comm
```

Sensor drivers can be built individually depending which one you are interested in. To start with, you can build the simulated sensors since they don't require you to connect any hardware.

```bash
$ cd osh-sensors
$ ./gradlew sensorhub-driver-fakegps:build
$ ./gradlew sensorhub-driver-fakeweather:build
```

You can also build all of them at once by running the following command in the `osh-sensors` folder:

```bash
$ ./gradlew build
```

This will build ZIP files (one for stable modules, one for dev) containing all sensor drivers in the `build/distributions` folder.

#### Build other modules

You can also clone other repositories of the project to build other types of modules.

The [osh-services](https://github.com/opensensorhub/osh-services), [osh-persistence](https://github.com/opensensorhub/osh-persistence) and [osh-security](https://github.com/opensensorhub/osh-security) repositories are probably of interest if you want to go further.

There are also some Android specific modules and a demo app in the [osh-android](https://github.com/opensensorhub/osh-android) repository if you are interested in deploying on Android.

#### Build ZIP distributions

Distribution build scripts are located in the [osh-distros](https://github.com/opensensorhub/osh-distros) repository:

```bash
$ git clone https://github.com/opensensorhub/osh-distros
```

You can build an installable ZIP package, complete with startup scripts by running:

```bash
$ cd osh-distros
$ ./gradlew -p osh-base build
```

The distribution is built in the `build/distributions` folder. You can unzip it and run OpenSensorHub using the launch script (it will run with the provided example configuration file, including some simulated sensors, storage databases and an SOS service). Please see the [Installation Guide](../install.md) for more details.


### Building with Eclipse

You can build and run the code using the Eclipse IDE by importing the Gradle projects using Eclipse Buildship plugin. 

#### Pre-requisites

Make sure you have the following Eclipse components installed:
  
  * Eclipse Neon or newer (the exact steps described here are for Neon)
  * Egit plugin for Eclipse (included in "Eclipse IDE for Java Developers" release)
  * Buildship plugin for Eclipse v2.0.0 or newer (Buildship is included in the "Eclipse IDE for Java Developers" release but you'll need to upgrade it to a newer version. See below for instructions)

#### How to Upgrade the Buildship plugin

  * Go to "Help > Install New Software" in the main menu
  * Add a new **Update Site** by clicking "Add..." at the top right
  * Set name to "Buildship Snapshots" and location to the following URL: https://builds.gradle.org/repository/download/Tooling_Master_IntegrationTests_Linux_Eclipse46Build/.lastSuccessful/update-site
  * Select the newly created update site in the list
  * Enter "guest" for login and password
  * Select "Buildship: Eclipse Plug-ins for Gradle" in the tree
  * Click "Next" to start the upgrade process to v2.0.0

#### Import the source code in your Eclipse workspace

First clone all the desired projects in your worskpace (see the [Getting the Code](#getting-the-code) section), for instance:

```bash
$ git clone --recursive https://github.com/opensensorhub/osh-core
$ git clone https://github.com/opensensorhub/osh-comm
$ git clone https://github.com/opensensorhub/osh-sensors
$ git clone https://github.com/opensensorhub/osh-distros
```

Then import each repository as a Gradle project using the following steps:

  * In the Package Explorer, right click and select "Import" from the popup menu
  * Open the "Gradle" category, select "Existing Gradle Project" and click "Next"
  * Click "Next" on the wizard welcome page
  * Set the "Project root directory" by browsing to the location of your local clone of the repository
  * Click "Finish"
  * All projects should be imported successfully and visible in the "Package Explorer". Everything should compile without error.

Since OSH consists of many modules, each of which will become a separate Eclipse project, we recommend that you group projects into Working Sets (for example, one working set per repo)


### Contributing

If you want to contribute, we feel the best way is that you create your own fork on GitHub, work on it, and when you have something working and tested, send us a Pull Request. To set this up, please follow the steps below:

#### Fork one or more repository of the project

The first step is to fork a repo by clicking the [Fork](http://help.github.com/articles/fork-a-repo/) button on GitHub. This will clone the original code to your own GitHub account so you can then modify it and/or add to it as you wish. For this you'll need to have a GitHub account (it can be done in 30s using your email address) and log into it.

Forking the project this way will allow you to send us [Pull Requests](http://help.github.com/articles/using-pull-requests/) via GitHub which makes it much easier for us to incorporate your contribution to the master branch. In addition, it creates a community around the software and lets others see what contributors are up to even before a patch is submitted. This can help you get the proper guidance when necessary.

#### Clone your GitHub repository

Clone your new GitHub repository locally by following the steps in the [Building from Source](#building-from-source) section except you'll be using your own fork URL (e.g. https://github.com/yourusername/osh-***) instead of the *opensensorhub* version.

#### Work on something new!

You can then start modifying the code and/or add new modules/features. We don't have coding guidelines yet but try to mimic the code that is already there. Don't forget to include Javadoc, especially on public parts of your APIs, and also inline comments explaining the different steps of your code.

Whether you're trying to fix bugs or adding a brand new functionality, don't hesitate to tell us early-on what you're planning to work on. We may be able to point you in the right direction or maybe to somebody who has similar needs than you.

You can start by reading the instructions to [Create a New Module](sensorhub-core/adding-new-modules.html) and [Add a New Sensor Driver](sensorhub-core/your-first-sensor.html) for instance.

Also see the [Eclipse Tips](#eclipse-tips) section if you encounter problems while creating a new module.

#### Get the latest updates from us

While you're working on your stuff, don't forget to pull changes from the main repository once in a while. This will greatly help us merge your changes into the main branch when we receive your Pull Request. You can either do that from command line git or within Eclipse:

##### Using the `git` command

First add a new remote pointing to the *opensensorhub* master branch (you only have to do that the first time). For example, for the osh-core repository:

```bash
$ git remote add upstream https://github.com/opensensorhub/osh-core
```

Then pull changes from the "upstream" remote:

```bash
$ git pull upstream master
$ git submodule update
```

_Note 1: The `submodule update` command is only required in the `osh-core` repo that has submodules._

_Note 2: that you may have to manually merge with your working copy if you have made conflicting changes._

##### Using Eclipse

First add a new remote pointing to the *opensensorhub* master branch (you only have to do that the first time):

  * Open the "Git Repositories" view (Window -> Show view -> Other -> Git)
  * Open the "sensorhub" repository, right click on "Remotes" and select "Create Remote"
  * Enter "upstream" as the remote name, select "Configure fetch" and click "OK"
  * Click the "Change" button next to the URI text box
  * Enter `https://github.com/opensensorhub/osh-core` as the URI and click "Finish"
  * Click "Save"

Then pull changes from the "upstream" remote:

  * Open the "Git Repositories" view (Window -> Show view -> Other -> Git)
  * Right click on the "osh-core" repository and select "Remote -> Fetch" in the popup menu
  * Select the "opensensorhub" remote in the "Configured remote repository" item and click "Finish"
  * Right click on the "Submodules" folder and select "Update Submodule" from the popup menu
  
You'll then eventually have to merge our changes with yours using the Egit merge command. Please see [Git Documentation](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging#Basic-Merge-Conflicts) for more details

Note: Also don't forget to import new Eclipse projects that may have been added since your last update. For this, follow these steps:

  * Right click in your workspace and select "Import..." in the context menu
  * Select "Existing project into workspace" from the "General" section and click "Next"
  * Browse to the folder where you cloned our repo (usually called "osh-core" for the core software)
  * Select the missing projects in the list (all the projects that are not already in your workspace should already be selected) and click "Finish"

#### Push your changes to your own repo

You can push your changes to your own GitHub repo at any time, even if your code doesn't work yet. Remember this is your own sandbox so you won't mess up anybody else code base. We actually recommend that you do that often since it will provide you a good backup of your work, with full history.

You won't be able to push directly to the opensensorhub repos directly since you don't have write permissions (not until you become part of the team anyway). 

##### Using the `git` command

To do this with git command line tool, first stage and commit your changes locally:

```bash
$ git commit -am "Your commit message"
```

and then push them to your remote GitHub repository:

```bash
$ git push
```

(Please see the [git online documentation](http://git-scm.com/book/en/v2) for more details and other ways to use git)

##### Using Eclipse

Within Eclipse, follow these steps:

To commit your changes locally:

  * Right click on one of the Eclipse project with a name starting with "sensorhub"
  * Select "Team -> Commit" from the popup menu
  * Enter a commit message and select files you want to commit
  * Click "Commit" (or "Commit and Push" if you want to commit locally and push to your remote repository in a single step)
  * If you have just pressed "Commit" you will see a arrow with a number on the right of the project names in the package explorer. This indicates that you have N local changes that need to be pushed to the remote repository (i.e. in git terms, your local repository is N commits ahead of your remote).

If you only want to push your last committed changes to your remote repository:

  * Right click on one of the Eclipse project with a name starting with "sensorhub"
  * Select "Team -> Push to Upstream" from the popup menu
  * Click OK

(Please see [Egit online documentation](http://wiki.eclipse.org/EGit/User_Guide) for more advanced functionality)

#### Contribute your code

When you feel you're ready to contribute all or some of your changes to the community, please send us a [Pull Request](http://help.github.com/articles/using-pull-requests/) via GitHub.

So that we can better evaluate your contribution, please describe your improvements in as much details as you can. We'll do our best to process *Pull Request* as fast as possible.

**Thanks in advance for your contribution!**



### Eclipse Tips

##### Update Gradle Settings

Everytime a change is made to the Gradle build scripts, the Eclipse project settings must be updated with the following steps:

  * Click one of the OSH module project
  * Select "Gradle > Refresh Gradle Project..." from the context menu

