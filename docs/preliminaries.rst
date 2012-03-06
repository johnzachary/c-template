.. _preliminaries:

Preliminaries
=============

This section provides some background information that you should
understand before using the template.


.. _prereqs:

Installing the necessary tools
------------------------------

As mentioned in the introduction, this template makes several
assumptions about which tools and libraries you're going to use with
your project.  You'll need to have all of the following installed before
you try to use the template.

Source code management
~~~~~~~~~~~~~~~~~~~~~~

We assume that you're going to use `git`_ as your source code management
software, and `git-flow`_ as your branch management strategy.  If you're
on a Mac, both of these are available in `Homebrew`_::

    $ brew install git
    $ brew install git-flow

.. _git: http://git-scm.com/
.. _git-flow: http://nvie.com/posts/a-successful-git-branching-model/
.. _Homebrew: http://mxcl.github.com/homebrew/

Build system
~~~~~~~~~~~~

We assume that you're going to use `CMake`_ as your build system.  This
is also available in `Homebrew`_::

    $ brew install cmake

.. _CMake: http://www.cmake.org/

If you're writing a library (as opposed to a command-line program), we
assume that you're going to install a `pkg-config`_ to file to tell your
users how to link with your library.  `pkg-config`_ is almost certainly
preinstalled on your machine.

.. _pkg-config: http://www.freedesktop.org/wiki/Software/pkg-config

Lastly, you're obviously going to need a working C compiler toolchain.
On the Mac, Apple has recently made it easier to download a `command
line C compiler`_ without having to install the full XCode IDE.

.. _command line C compiler: http://kennethreitz.com/xcode-gcc-and-homebrew.html

Signing releases
~~~~~~~~~~~~~~~~

If you're going to release your project's code publicly, it's always a
good policy to cryptographically sign each release.  git and git-flow
support this via *signed tags*.  This requires that you have `GnuPG`_
installed, and that you have a valid secret key in your GnuPG keyring.
The details of setting up a GnuPG keyring are outside the scope of this
document; however, the Apache Foundation has a good `overview`_ that you
can follow if needed.

GnuPG is available in Homebrew, and also might already be installed if
you've installed the `GPG plugin`_ for Apple Mail::

    $ brew install gnupg

.. _GnuPG: http://www.gnupg.org/
.. _overview: http://www.apache.org/dev/openpgp.html
.. _GPG plugin: http://www.gpgtools.org/

Documentation
~~~~~~~~~~~~~

You're going to document your code right?  Good.  We're assuming that
you're going to use `Sphinx`_ to do so.  (That's what was used to
produce the documentation you're reading, for instance.)  We chose
Sphinx because it has the best cross-language support of the various
tools we looked at.

.. _Sphinx: http://sphinx.pocoo.org/

Sphinx is *not* available in `Homebrew`_, unfortunately.  You should be
easy to ``easy_install`` it, however::

    $ easy_install -U Sphinx

(It's also possible to use a more complex setup using ``pip`` and
``virtualenv``, but this is left as an exercise for the reader.)

Third-party libraries
~~~~~~~~~~~~~~~~~~~~~

Many of the librarys and programs that we've built using this template
use `libcork`_ as a basic cross-platform C library.  You can do this as
well, though its use is completely optional.

.. _libcork: http://github.com/redjack/libcork/

We also assume that you're going to write test cases for your project,
and that you'll use the `check`_ library to implement those test cases.
This library is also available in `Homebrew`_::

    $ brew install check

.. _check: http://check.sourceforge.net/


Variable reference
------------------

Throughout the instructions in this documentation, we're going to use
environment variables to refer to several placeholders that you'll have
to fill in.  You can either define these as actual environment variables
(which will let you copy/paste the shell commands in the instructions
verbatim), or manually replace each reference to an environment variable
with its value.

.. envvar:: PROJECT_SLUG

   The “slug” name of your project.  A slug name should be short, and
   should follow the same restrictions that identifiers have in most
   programming languages: no space, only alphanumeric characters (plug
   hyphen and underscore).  As an example, the template itself has the
   slug ``c-template``.

.. envvar:: PROJECT_NAME

   The longer, more human readable name of your project.  Sometimes,
   this will be the same as :envvar:`PROJECT_SLUG`.  For instance, the
   template itself has the name ``c-template``.

.. envvar:: REPO_DIR

   The location of your code repository on your local machine.  Assuming
   that you have a separate subdirectory in your home directory, where
   you keep all of your checked-out repositories, this will have the
   form ``~/git/${PROJECT_SLUG}``, ``~/Projects/${PROJECT_SLUG}`` or
   similar.

.. envvar:: REMOTE_REPO_URL

   The git URL of the remote repository that you'll push your code
   changes to.  For `GitHub`_ projects, this will be something like::

       git@github.com:${GITHUB_ORGANIZATION}/${PROJECT_SLUG}.git

.. _GitHub: https://github.com/
