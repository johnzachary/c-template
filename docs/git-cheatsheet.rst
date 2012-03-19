.. _git-cheatsheet:

Git cheat sheet
===============

This section provides a cheat sheet of `git`_ commands for common functions
The goal is provide these commands in a single easily referenced
document.  The document assumes you have referenced :ref:`tools and
utilities <preliminaries>` installed (if not, try ``brew install
<tool>`` on Mac OS X or ``apt-get install <tool>`` on Ubuntu).
Note that in addition to git itself, we assume that you have `git-flow`_
and `hub`_ installed.

.. _git: http://git-scm.com/
.. _git-flow: http://nvie.com/posts/a-successful-git-branching-model/
.. _hub: http://defunkt.io/hub/

.. note::

   If you follow the installation instructions for the `hub`_ tool, you
   might have added an alias to your shell configuration that lets you
   use ``hub`` as a wrapper for the ``git`` command.  If you've done
   this, replace all instances of ``hub`` below with ``git``.  In the
   instructions, we distinguish between the two to make it clear which
   commands are handled by the core git program, and which are handled
   by the GitHub extension wrapper.


Initially cloning a repository
------------------------------

This code assume that you fork a repository from an *upstream
repository* into a *forked repository*.  The upstream repository will be
the one that's maintained by the project's maintainers; for public
RedJack projects, this will be the [``redjack`` GitHub
organization](https://github.com/redjack/).  In your local filesystem,
the upstream repository will be called ``origin``, and your fork will
have the same name as your GitHub username.  (You can find the upstream
URL by loading the upstream repository in your web browser.)

::

    $ git clone <upstream URL>
    $ git checkout master
    $ git checkout develop
    $ git flow init
      [accept all defaults]
    $ hub remote add -p <github-username>
    $ git fetch <github-username>

Use ``git remote -v`` to list all tracked remote repositories. 


Visualizing a local clone
-------------------------

The history of a git repository is represented by a graph of commits, or
snapshots.  It can often be *very* useful to visualize the current state
of a repository's graph.  There are several tools that do this.  On Mac
OS X, you can use `GitX`_ or `Git Tower`_.  GitX has the slight
advantage that you can ask it to show all branches simultaneously from
the command line::

    $ gitx --all

.. _GitX: http://gitx.frim.nl/
.. _Git Tower: http://www.git-tower.com/

You can also get the same graph visualization on the command line::

    $ git log --oneline --decorate --all --graph --color

These commands are both very useful; you'll almost certainly want to
alias one or both of these in your shell to make it easier to run them.
(The author uses ``gxx`` and ``gx`` for the GitX and command-line
versions, respectively.)


Adding a feature to an existing repository
------------------------------------------

Before you begin work on the new feature or bug fix, you should make
sure that there is a GitHub issue that describes the changes you would
like to make.  For bug fixes, the issue will most likely exist already.
For new features, you might have to create one.  You should create the
issue in the upstream repository's GitHub page.  You should also assign
the issue to yourself, to prevent other developers from duplicating
effort.

Once the issue is created on the GitHub web site, you should create a
*topic branch*.  This is where you'll make any changes needed to
implement the new feature or bug fix.  The git-flow command greatly
simplifies the process of creating a topic branch::

    $ git flow feature start <feature-name>

This automatically creates a branch called ``feature/<feature-name>``,
and bases it off of the upstream repository's ``develop`` branch.  At
this point, you can add and edit files as needed to implement your
change.  From time to time, you should commit your changes and push them
to your personal fork, so that other developers can see what you're
working on::

    $ git add <new-files>       # Use if new files are added to the branch
    $ git commit                # Might use the -m option for short messages
    $ git push <github-username> feature/<feature-name>

Once you have an initial draft of the changes finalized, you should
attach your topic branch to the corresponding issue.  Doing this
transforms the issue into a *pull request*.  You use the ``hub``
command's ``pull-request`` feature to do this::

    $ hub pull-request -i <issue-number> -b develop

You can get the ``<issue-number>`` by looking at the issue's page on the
GitHub site.  At this point, the project maintainers and other
developers will review your code.  If they request that you make further
changes, follow the same process as above: edit the files as necessary,
make new commits, and push the new changes to your local clone.  Once
the maintainers are happy with your contributions, they'll merge your
topic branch into ``develop``.


Merging an upstream repository with a local branch
--------------------------------------------------

From time to time, you'll need to make sure that your local clone has
the latest changes that have been merged into ``develop``::

    $ git checkout develop
    $ git fetch origin
    $ git merge origin/develop
    $ git push <github-username> develop


Resetting a branch to the previous commit state
-----------------------------------------------

Use the following commands when you screw up some edits to a branch and
want to revert to the previous commit::

    $ git checkout <branch-name>
    $ git reset --hard HEAD^


Deleting a branch both locally and remotely
-------------------------------------------

If you create a branch to experiment with or a feature branch is merged
into the main repository, use the following commands to remove the
branch::

    $ git branch -d <branch-name>      # Note: you may have to use ``-D``
    $ git push <github-username> :<branch-name>
