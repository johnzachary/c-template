.. _organization:

Project organization
====================

This section describes the expected layout of directories and files in a
project that uses this template.  It also describes some of the features
that are automatically set up for you by the template.


Directory layout overview
-------------------------

The following is an overview of the important directories in the project
template:

*include*
   Should contain any public header files for your project

*src*
   Should contain C source code for any libraries and command-line
   utilities in your project

*docs*
   Should contain Sphinx documentation for your project

*tests*
   Should contain your project's test suite


Public include files
--------------------

If your project provides a C library, you should place the public header
files for your library into the project's *include* directory.  The
CMake build scripts will automatically find and install any *\*.h* file
in this directory.  Any directory structure that you create underneath
*include* will be reproduced when installing into ``${PREFIX}``.  So, if
your project contains the following files::

    include/coollib.h
    include/coollib/basics.h
    include/coollib/extras.h
    include/coollib/io.h

Then your ``make install`` target will install the following::

    ${PREFIX}/include/coollib.h
    ${PREFIX}/include/coollib/basics.h
    ${PREFIX}/include/coollib/extras.h
    ${PREFIX}/include/coollib/io.h


Source code
-----------

The *src* directory should contain one subdirectory for each library and
command-line program in your project.  Individual source files should
along belong to a single library or program.  (If you need to share code
across multiple programs, but don't want to include it in an installed
shared library, you can create a “helper” static library, which is
linked into each program, but which isn't installed on its own.)

You should also provide a pkg-config file for each public shared library
in your project.  This pkg-config file contains details that other users
will use to set up the include and linker paths when using the library.
(If your project contains several public shared libraries, each one
should get its own pkg-config file.)

The default *src/CMakeLists.txt* file contains skeleton CMake rules for
building a shared library and installing its corresponding pkg-config
file.


Documentation
-------------

The *docs* directory should contain a collection of reStructuredText
files that will be used to produce the `Sphinx`_ documentation for your
project.  The *docs/conf.py* will have been edited when the project was
:ref:`first created <new-project>`; in most cases, you won't have to
edit this file any further.  Instead, you'll just create additional
*.rst* files for each section of documentation, and add an entry to the
table of contents in *docs/index.rst* for each new *.rst* file.

.. _Sphinx: http://sphinx.pocoo.org/

If Sphinx is installed, the Makefiles produced by CMake will
automatically build the documentation whenever you run ``make`` in the
build directory.  You can see the built documentation by opening the
``${BUILD_DIR}/docs/html/index.html`` file.  This lets you preview the
documentation without having to install the package.

The Makefile will also automatically install the built documentation
when you run ``make install``.  The documentation will be placed into

::

    ${PREFIX}/share/doc/${PROJECT_SLUG}

Linking to external documentation sets
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you're writing a library that depends on another library that
provides Sphinx documentation, you can use `Intersphinx`_
cross-references to add links between the different documentation sets.

.. _Intersphinx: http://sphinx.pocoo.org/latest/ext/intersphinx.html

There are three steps you need to perform to turn on Intersphinx support.

1. Load the Intersphinx extension plugin by adding

   .. code-block:: python

       'sphinx.ext.intersphinx'

   to the ``extensions`` array in *docs/conf.py*.

2. Uncomment and/or edit the ``intersphinx_mapping`` dictionary in
   *docs/conf.py*.  This is where you define the external Sphinx
   documentation sets that you want to link to.  For instance, to link
   with `libcork`_\ 's documentation, you would add:

   .. code-block:: python

      intersphinx_mapping = {
          'libcork': ('http://libcork.readthedocs.org/en/latest/', None),
      }

   The ``'libcork'`` part is the short name that you'll use in your
   Intersphinx references to refer to the external documentation set.
   The URL is the default location of the external documentation.  (For
   private projects, this doesn't have to be a publicly available URL;
   it just needs to be a URL that will be available to anyone who is
   allowed to download and build this project.)

3. Update *docs/CMakeLists.txt* to call the ``find_prereq_doc`` helper
   macro for each external documentation set you want to link to.
   (Search for the comment that begins “If your Sphinx documentation
   references” to find where to call the macros.)  The parameter to the
   macro should be the name of the pkg-config file for the library whose
   documentation you're linking to.

Together, these three steps let you refer to external documentation.  If
there is a local copy of the library (and its documentation) installed,
the links will point to the local copy.  If not, the links will fall
back on the publicly available copy at the URL provided in your
``intersphinx_mapping`` dictionary.  (The local copy of the
documentation is pointed to by the ``sphinxdir`` variable in each
pkg-config file produced by the template.)


Tests
-----

You should implement test cases for every library and program in your
project.  Without exception!  Seriously, write some test cases.

The template supports two kinds of test cases: unit tests based on the
`check`_ library, and command-line tests that let you check the stdout
and stderr of an arbitrary command.  (Presumably, you'll use the first
to check the individual functions and types of a shared library, and the
second to test the overall behavior of a command-line program.)

.. _check: http://check.sourceforge.net/

Library tests
~~~~~~~~~~~~~

To write a library test, you create a new :file:`test-{something}.c`
file in the *tests* directory, and add a call to the ``make_test`` macro
in *tests/CMakeLists.txt*.  The default template links these test
programs with the ``check`` library; you should make sure the
``target_link_libraries`` call in the macro definition also links in to
any libraries in your project, as well as any other third-party
libraries needed by the test case.  (You shouldn't need to explicitly
mention third-party libraries needed by the project's library; CMake
will automatically include any transitive dependencies that it can
determine.)

With these definitions in place, the ``make test`` target in your
project's Makefile will automatically run each of the test programs that
you've defined.  If a test fails, you can run it directly to see its
output::

    $ tests/test-something

(This should be run from the top-level build directory.)

Often you'll have several test programs that share common code.  A
useful strategy is to create a *tests/helpers.h* file to contain this
common code, and to include this file in each of your test programs.

Command-line tests
~~~~~~~~~~~~~~~~~~

You can also write test cases that execute an arbitrary command, and
verify the contents of its stdout and stderr streams.  (You can also
provide an arbitrary stdin stream that will be passed in to the
program.)

Each test case is defined by a subdirectory of the project's *tests*
directory.  Test case subdirectories can be nested; this lets you
group several related test cases together.   A common pattern is that
you'll have a :file:`tests/{something}` directory for a particular
program (or a particular set of options for a program), and separate
test case subdirectories underneath that for a variety of inputs.

Any subdirectory that contains a file named *command* is considered to
be a test case directory.  Each test case directory should define the
following files:

*command*
   The command that should be run to perform the test.  This can be an
   arbitrary shell command, including pipes and redirections.  It will
   be executed by the Bourne shell (*/bin/sh*).

*in*
   If present, the contents of this file will be passed into the command
   on stdin.  This file is optional; if you don't provide one, then
   we'll hook */dev/null* up to the command's stdin.

*out*
   If present, we'll verify that the stdout stream of the command
   matches the contents of this file.  Only an exact match is considered
   successful.

*err*
   If present, we'll verify that the stderr stream of the command
   matches the contents of this file.  Only an exact match is considered
   successful.

These test subdirectories are automatically found by the template's
CMake scripts; you don't need to edit *tests/CMakeLists.txt* when you
add a new command-line test case.  The ``make test`` target will
automatically run the test case for each test case directory that CMake
can find.

If you need to construct paths to test data files in the repository, you
can use relative paths: the test cases will be run from the top-level
build directory, and we assume that this will be a direct subdirectory
of your top-level source directory.

If you see that a particular test fails, you can run it directly using
the ``run-test`` script.  (This is the same script that CMake uses in
the test harness.)  You run this script from within your top-level build
directory.  It's usage is::

    $ ./run-test ${PATH_TO_TEST_DIRECTORY}

(``PATH_TO_TEST_DIRECTORY`` will be something like
``../tests/test-case-1``, since you'll be running the script from within
the build directory, while the test case is defined in the the source
directory.)  ``run-test`` will display a diff of the actual and expected
stdout and stderr streams, which should help you debug the test case.

When you create a new test case, or if you need to update the expected
stdout or stderr for an existing test case, you can use the ``train``
script.  Like ``run-test``, you run this from your build directory::

    $ ./train ${PATH_TO_TEST_DIRECTORY}

This runs your test case, just like ``run-test`` would, but instead of
verifying the stdout and stderr streams, it assumes that the current
output is correct.  It will collect the stdout and stderr for you, and
save it into the appropriate *out* and *err* files in the souce test
case directory.
