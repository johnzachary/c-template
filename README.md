Installation instructions
=========================

The LIBRARY_NAME library is written in ANSI C.  It uses cmake as its
build manager.


Prerequisite libraries
----------------------

To build LIBRARY_NAME, you need the following libraries installed on
your system:

  * pkg-config
  * check (http://check.sourceforge.net)
  * [ADD OTHERS AS NEEDED]


Building from source
--------------------

The LIBRARY_NAME library uses cmake as its build manager.  In most
cases, you should be able to build the source code using the following:

    $ mkdir build
    $ cd build
    $ cmake .. -DCMAKE_INSTALL_PREFIX=$PREFIX
    $ make
    $ make test
    $ make install

You might have to run the last command using sudo, if you need
administrative privileges to write to the $PREFIX directory.
