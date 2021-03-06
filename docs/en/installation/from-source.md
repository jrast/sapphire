# Installation from Source Control

## Introduction

SilverStripe core (and most of its modules) use git to version control their source code.
We require you to use this method for any [patch contributions](/misc/contributing),
to ensure you're working on the latest codebase, and the problem you're looking at
is not already fixed.

For getting a project up and running quickly with a release, you are typically best off
with the official [silverstripe.org/download](http://silverstripe.org/download). 
If you want to get the "latest and greatest" pre-release code (either
on a release branch, or on "trunk"), you need to use our version control.

<div class="warning" markdown="1">
**Warning**: These instructions are for intermediate to advanced users only,
and require some knowledge of version control and using command line tools.
</div>

See [frequently asked questions](/installation/from-source#frequently-asked-questions) below.

## The core and its parts

SilverStripe core is currently hosted on [github.com/silverstripe](http://github.com/silverstripe). The core consists of four parts:

 * The `installer` project ([github.com/silverstripe/silverstripe-installer](http://github.com/silverstripe/silverstripe-installer))
 * The `framework` module ([github.com/silverstripe/sapphire](http://github.com/silverstripe/sapphire))
 * The `cms` module ([github.com/silverstripe/silverstripe-cms](http://github.com/silverstripe/silverstripe-cms))
 * A sample theme called `simple` ([github.com/silverstripe-themes/silverstripe-simple](http://github.com/silverstripe-themes/silverstripe-simple))

First, you'll have to decide what you want to do with your project: 

 * [Option 1: Start a new project on your own](/installation/from-source#option-1-installation-for-new-projects)
 * [Option 2: Contribute back patches to SilverStripe](/installation/from-source#option-2-installation-for-contributions)

These options aren't very clear cut, you can mix-and-match approaches to suit your needs
(e.g. core modules are downloaded as files, but your own modules are still managed through `svn:externals`).

### Requirements ###

 * A **git client** to check out the core repositories, see ["Getting started with Git and Github"](http://help.github.com/).
 * A  **webserver+database environment** to run SilverStripe (see [server requirements](/misc/server-requirements)).
 * The **php commandline utility** (to run scripts in the `tools/` folder)
 * (optional) **Piston** ([website](http://piston.rubyforge.org)): A thirdparty tool to manage files from an external repository. It is our recommended way to start your own project, and still provide an easy way to update from our repository. You will need Ruby and the ["Rubygems"](http://rubygems.org/) package manager to install it: `gem install piston`
Note for Windows users: The installation process assumes a Linux/Unix/OSX system.
Most commands are the same for Windows, but you will have to use the `*.bat` scripts instead for anything in the `tools/` folder
(e.g. `tools/new-project.bat` instead of `tools/new-project`).

<div class="warning" markdown="1">
Scripts in the `tools/` folder are still under development and might change over time.
</div>

## Option 1: Installation for new projects ##

Your own projects are typically hosted in a version control system of your choice,
with SilverStripe files copied or linked into that repository. We assume you already
have an empty repository set up, either in git or subversion. 

If you don't use version control, we recommend that you stick to the official [silverstripe.org/download](http://silverstripe.org/download) instead.

### Step 1: Getting the installer

 * Create a new project repository in your own version control (we assume the working copy folder is called `my-silverstripe-project/`)
 * Download and extract silverstripe-installer [master](https://github.com/silverstripe/silverstripe-installer/zipball/master) 
   or the [latest release](https://github.com/silverstripe/silverstripe-installer/zipball/2.4).
 * Add and commit the files to your repository

### Step 2: Installing phing with git version control support ###

Run the following commands to install the Phing build system:

	sudo pear channel-discover pear.phing.info
	sudo pear install phing/phing
	sudo pear install VersionControl_Git-0.4.4

### Step 3: Getting the required modules ###

Run the following command to download all core dependencies using [Phing](http://www.phing.info/):

	phing update_modules

This will add `framework`, `cms` and the `simple` theme to your project.

### Step 4: Committing the modules ###

Some files in your project will be unversioned after running the Phing script
and need to be added to your own repository. The commands depend on your repository type:

	# for subversion
	cd my-silverstripe-project/
	svn add *
	svn commit -m "adding configuration files generated by phing"

	# for git
	cd my-silverstripe-project/
	git add .
	git commit -m "adding configuration files generated by phing"

### Step 5: Switch branches ###

The Phing script makes it easy to switch branches and/or modules. Just edit the `dependent-modules` file in
your project's root folder. This file contains documentation about how to specify which modules (and which branches)
you want to include in the project. After updating the file run the `phing update_modules` command again.

Note: Phing can do some more useful things. Run `phing help` to see everything you can do with it.

### Step 6: Running the web-based installer ###

You can now run through the web-based installer for your operating system of choice ([instructions](/installation)).




## Option 2: Installation for contributions ##

This way of installing SilverStripe will allow you to commit back directly to version control for a module.
We recommend it for module and core development (as opposed to development on a client project).

### Step 1: Forking the installer and projects

First of all, you need to [fork](http://help.github.com/forking/) the installer and modules
into your own github account, so you can push changes (github.com/silverstripe is only writeable by the core team).

A fork gives you write access to your own repository copy, and makes it efficient to
contribute back changes. This approach won't add the modules to version control in the containing `installer` folder,
which means it only works for local development.

Note: You only need to fork the modules you actually plan to work on,
feel free to keep the original repository URLs for all other modules.

### Step 2: Getting the installer and required modules ###

To get started you just need to check out your fork of the `installer` project (this will take a minute or two).
This folder will be your webroot, and eventually contain all other modules.
Please replace `<username>` below with your github username.

	git clone git@github.com:<username>/silverstripe-installer.git my-silverstripe-project
	cd my-silverstripe-project
	git clone git@github.com:<username>/sapphire.git framework
	git clone git@github.com:<username>/silverstripe-cms.git cms
	git clone git@github.com:<username>/silverstripe-simple.git themes/simple

Now you need to add the original repository as `upstream`, so you can keep your fork updated later on.

	cd my-silverstripe-project
	(git remote add upstream git://github.com/silverstripe/silverstripe-installer.git && git fetch upstream)
	(cd framework && git remote add upstream git://github.com/silverstripe/sapphire.git && git fetch upstream)
	(cd cms && git remote add upstream git://github.com/silverstripe/silverstripe-cms.git && git fetch upstream)
	(cd themes/simple && git remote add upstream git://github.com/silverstripe-themes/silverstripe-simple.git)

Now that you're set up, please read our ["Collaboration on Git"](../misc/collaboration-on-git) guide,
as well as our general ["Contributor guidelines"](../misc/contributing).
Please read ["Module installation"](/topics/modules) to find out how to install additional modules like `blog` or `forum` from source.

### Step 3: Committing the modules ###

You don't need to commit the module code into the repository, as our project is only for local development.
Changes within the module code are committed back directly to their own repository, not into the `installer` project. To the `installer` project, these modules are unversioned files (although you can explicitly add them to `.gitignore` as well).

### Step 4: Switch branches ###

By default, the "master" is checked out, which contains the latest code.
You can optionally select a ["release branch"](https://github.com/silverstripe/silverstripe-installer/branches) to work on. Any work happens on a local branch, that you have to create first:

	cd my-silverstripe-project
	git checkout -b 2.4 origin/2.4
	(cd framework && git checkout -b 2.4 origin/2.4)
	(cd cms && git checkout -b 2.4 origin/2.4)
	(cd themes/simple && git checkout -b 2.4 origin/2.4)
	# repeat for all modules in your project...

After creating the local branch, you can simply switch between branches:

	cd my-silverstripe-project
	git checkout 2.4
	(cd framework && git checkout 2.4)
	(cd cms && git checkout 2.4)
	(cd themes/simple && git checkout 2.4)
	# repeat for all modules in your project...

To switch back to master:

	cd my-silverstripe-project
	git checkout master
	(cd framework && git checkout master)
	(cd cms && git checkout master)
	(cd themes/simple && git checkout master)
	# repeat for all modules in your project...

You can't switch branches if your working copy has local changes (typically in `mysite/_config.php`).
Either revert these changes before switching, or temporarily store them with `git stash`.
Once you switch back you can retrieve these changes via `git stash pop` (see further [instructions on `git stash`](http://progit.org/book/ch6-3.html)).

### Step 5: Running the web-based installer ###

You can now run through the web-based installer for your operating system of choice ([instructions](/installation)).

## Updating from source ##

The `tools/` scripts provide an easy start, but don't allow you to add, remove or update modules.
Please read the following instruction on how to udpate modules and the installer depending
on your setup.

### Updating the installer ###

If you've done a straight `git clone` as described above, the update process is very simple:

	cd my-silverstripe-project/
	git pull origin

If you have copied the installer files into a new project, we recommend to repeat the copying process manually.

### Updating modules via git ###

In case you chose the "Installation for contributions" option, all modules in your project
will be standard git repositories, and you can update them as usual.

	cd my-silverstripe-project/framework
	git pull

### Updating modules via piston or download ###

For the "Installation for a new project" option, modules like `framework` or `cms`
are added as plain files without a direct link to their originating repository.
If these plain files are managed by piston, the update process is simple:

	cd my-silverstripe-project
	piston update framework
	# Use "svn" instead of "git" for subversion repositories
	git add framework/*
	git commit -m "udpated dependencies"

For file downloads without piston, you can simply download the source code again and replace it.

## Contributing changes from piston ##

If you have started your own project, and made improvements to code
managed through piston within this - great! While it is a bit easier
to contribute code from direct git repositories, there is another way
to get these changes back to the original module repository: [piston-export](http://github.com/sminnee/piston-export).

This script finds all changes to a certain module and exports them as patch
files that you can send to the module author (either as "pull requests" from your own fork,
or as flat files through tickets or emails).

## Manual installation of other modules ##

Modules listed on [silverstripe.org/modules](http://silverstripe.org/modules) can be hosted
in any version control system (typically subversion or git). Please read the module
page for source code locations and installation instructions. The general process of
[module installation](/topics/modules) is documented as well.

A good place to start looking for the source code of popular modules are the [github.com/silverstripe](http://github.com/silverstripe)
and [github.com/silverstripe-labs](http://github.com/silverstripe-labs) project pages.

## Using Piston ##

See [piston.rubyforge.org](http://piston.rubyforge.org/import.html).

## Frequently Asked Questions ##

 * **I'm not a big fan of git, can I use Subversion for my own projects?**: Of course, you can manage your own project files any way you want.
   To get SilverStripe modules from version control, you will have to use git to check out the code, and then add it to your own version control.
 * **Can I use svn:externals?**: If your project is hosted on subversion, you can add your own svn:externals as usual.
   To include most SilverStripe modules and themes from github, you have two options: Copying the files directly into your
   own version control, or use ["piston"](http://piston.rubyforge.org) to manage this for you.
 * **Some modules I use are still in subversion, can I mix and match with git?**: Yes, through ["piston"](http://piston.rubyforge.org).
 * **I've cloned a module repository and now I want to make changes to it (that shouldn't go into the main version)**: 
   You can either run `piston import` and then apply your changes to the imported source, or edit your "git remote" for those modules.
 * **Why don't you use git submodules or subtree merging instead of piston?**: In our experience, [Git submodules](http://progit.org/book/ch6-6.html) only work well if used in a readonly way, not for committing to the submodule repository. 

## Related ##

 * [Contributing: Submitting patches](/misc/contributing)
 * [Collaboration on Git](/misc/collaboration-on-git)
 * [Pro git - free online book](http://progit.org/book/)
 * [Git cheat sheet - github.com](https://github.com/guides/git-cheat-sheet)
 * [Git - SVN Crash Course - git.or.cz](http://git.or.cz/course/svn.html)
