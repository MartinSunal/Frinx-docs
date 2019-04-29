
Developer Guide
===============

*  `Repositories <#repositories>`__
*  `Cloning a repository <#cloning-a-repository>`__
*  `Add and commit <#add-and-commit>`__
*  `Commit message - subject <#commit-message---subject>`__
*  `Commit message - body <#commit-message---body>`__
*  `Pushing changes <#pushing-changes>`__
*  `Creating a Code-Review in Gerrit <#creating-a-code-review-in-gerrit>`__
*  `Branches <#branches>`__
*  `Update and merge <#update-and-merge>`__
*  `Log <#log>`__
*  `Replace local changes <#replace-local-changes>`__
*  `Other Git commands <#other-git-commands>`__
*  `Revert a change <#revert-a-change>`__

The FRINX SBE uses Git and Gerrit to manage version control. Version control is a system that records changes to a file or set of files over time so that you can recall specific versions later.

Gerrit is a code review project developed by Google. It is intended to provide a lightweight framework for working with Git, and makes it easy to review every commit before it is accepted into the code base. Changes are uploaded to Gerrit but are not applied to the project until they’ve been reviewed and accepted.

Hence Gerrit makes it simple for all committers on a project to ensure that changes are checked before they’re applied. Gerrit captures notes and comments about the changes to enable discussion of the change. This also creates a lasting record of the conversation.

Below we take you through how to configure git and some of the standard functionality you can use.

**Downloading and setting up Git**

To download Git on Ubuntu:

.. code-block:: guess

   sudo apt-get install git

For other installations see https://git-scm.com/download/linux

When you first install Git you must set your name and email address, because every Git commit uses this information. To set these properties use the following commands:

.. code-block:: guess

   git config --global user.name "User Name"  
   git config --global user.email "username@frinx.io"

To check your Git config settings

.. code-block:: guess

   git config --list

Git uses your system's default text editor. To change this, for example to emacs

.. code-block:: guess

   git config --global core.editor emacs

Git/Gerrit Workflow
===================

Repositories
------------

The purpose of Git is to manage a project, or a set of files, as they change over time. Git stores this information in a data structure called a repository. what is. can either create or clone

Your local repository consists of three "trees" maintained by git:

The first is your **Working directory** which holds the actual files.
The second is the **Index** which acts as a staging area.
The third is the **HEAD** which points to the last commit you've made.

The flow of changes is: **Working directory** > **Index (stage)** > **HEAD**

Cloning a repository
--------------------

The first thing we need to do is access the source that we’re going to be modifying. As with any Git project you do this by cloning the central repository that Gerrit is hosting. e.g.

Format:

.. code-block:: guess

   git clone username@host:/path/to/repository



Example:

.. code-block:: guess

   git clone https://[customername]@gerrit.frinx.io/sbe



Add Gerrit remote reference (in git 2.0)

.. code-block:: guess

   git remote add gerrit username@gerrit.frinx.io:29418



Before the changes can be reviewed in Gerrit, you need to install and configure git-review from the command line (this only needs to be done once)

.. code-block:: guess

   sudo apt-get install git-review



Configure Git review

.. code-block:: guess

   git config --global gitreview.remote origin  
   git review -s  



Add and commit
--------------

The next step is to make the change and commit it locally. (Gerrit isn't involved here - we just use standard editing and Git).

If you have not cloned an existing repository and want to connect your repository to a remote server, you need to add it with git remote add origin. Now you are able to push your changes to the selected remote server

To propose a changes (add it to the Index) use

.. code-block:: guess

   git add [filename]   



or

.. code-block:: guess

   git add *



Adding is the first step in the basic Git workflow. The next stage is to commit the changes:

.. code-block:: guess

   git commit -m "Commit message"  



This commits the file to the **HEAD**. Note that no changes are made to the remote repository at this stage. This only happens push the change - see the next step below).

The commit message is text which you enter in order to explain what changes you made in the commit. Here are some guidelines on entering text for the commit message:

Commit message - subject
------------------------

Use the imperative mood (e.g. "Update file1"; not "Updates file1", "Updating file1", nor "Updated file1").

Commit message - body
---------------------

Use the body of the commit message to describe your change in more detail. It is useful to separate the body from the subject with an empty line.

-- Give an overview of why you're committing this change
-- Explain what the commit changes
-- Explain any new design choices made.

Pushing changes
---------------

After you’ve made your change and committed it locally the next step is to push it to Gerrit so that it can be reviewed using the Gerrit User Interface. This is done with a Git push to the Gerrit server (the command is shown below).

To send changes from the *HEAD** of your working copy to your remote repository, use

.. code-block:: guess

   git push origin master  



origin is the name of the remote repository. master is the name of the branch. You can change master to whatever branch you want to push your changes to (see more info on branches further below).

Creating a Code-Review in Gerrit
--------------------------------

Now that the commit has been pushed to the remote repository, it is ready for review in Gerrit.

Gerrit review works by using a voting system. Code review votes dictate whether the change can be submitted.

The highest vote (+2) enables submitting. The lowest vote (-2) blocks the change from being submitted. It can’t be overruled by other reviewers.

Non-committers can typically vote with -1 and +1 in Code-Review to indicate an opinion it is up to the decision of those with +2 power to make the overall decision.

If the change is not accepted the creator is required to rework it. Once the Publish Comments button has been clicked, the cover message and any comments on the files become visible to all users.

Branches
--------

Branches are used to develop features isolated from each other. The master branch is the "default" branch when you create a repository. You can use other branches for development and then merge them back to the master branch when you have completed your work.

To create a new branch named "feature_a" and switch to that branch use

.. code-block:: guess

   git checkout -b feature_a



To switch back to the master branch

.. code-block:: guess

   git checkout master



To delete the new branch

.. code-block:: guess

   git branch -d feature_x



Update and merge
----------------

To update your local repository to the newest commit (fetching and merging remote changes), use

.. code-block:: guess

   git pull



To merge another branch into your active branch (e.g. master), use

.. code-block:: guess

   git merge <branch>



In both cases Git tries to auto-merge changes. Unfortunately, this is not always possible and results in conflicts. You are responsible to merge those conflicts manually by editing the files shown by Git. After changing, you need to mark them as merged with

.. code-block:: guess

   git add <filename>



Before merging, you can preview changes using

.. code-block:: guess

   git diff [source_branch] [target_branch]



Log
---

You can study repository history (by viewing a log of all commits starting from HEAD back to the initial commit) using

.. code-block:: guess

   git log



You can add a lot of parameters to configure the log as you wish. For example to see only the commits of a particular author

.. code-block:: guess

   git log --author=john



To see a very compressed log where each commit is one line

.. code-block:: guess

   git log --pretty=oneline



To see only which files have changed

.. code-block:: guess

   git log --name-status



For more info, see git log --help

Replace local changes
---------------------

If you made a mistake you can replace local changes using

.. code-block:: guess

   git checkout --[filename]



This replaces the changes in your **Working directory** with the last content in **HEAD**. Changes already added to the **Index**, as well as new files, will be kept.

If you would rather abandon all your local changes and commits, you can fetch the latest history from the server and point your local master branch at it

.. code-block:: guess

   git fetch origin
   git reset --hard origin/master



Read More: [Git Commit Messages][2]

Other Git commands
------------------

To show which files have changed between the current project state and HEAD.

.. code-block:: guess

   git status



To show the difference between HEAD and the current project state

.. code-block:: guess

   git diff  



To mark files to be renamed

.. code-block:: guess

   git mv source_filename destination_filename



To mark files to be moved

.. code-block:: guess

   git mv source_filename destination_directory



To mark files to be deleted

.. code-block:: guess

   git rm filename



Revert a change
---------------

If you need to revert a change use git revert **Never use git reset!** Use git log to get the commit number of the change

.. code-block:: guess

   git log -1



The response could include for example: commit 7d7356151634a6c258f3d9eb2ce592df016aa494 Author: [removed] Date: Wed Jun 15 17:01:50 2016 +0200

Then use git revert with the commit number

.. code-block:: guess

   git revert 7d7356151634a6c258f3d9eb2ce592df016aa494

