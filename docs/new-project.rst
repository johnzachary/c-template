.. _new-project:

Creating a new project
======================

The primary thing that you'll do with this template is to create a new
project.  We're assuming that you've already made some important
logistical decisions, such as what to call the project, and where you're
going to host it.  Also, we assume that you've installed all of the
necessary :ref:`prerequisite tools and libraries <prereqs>`.

1. *Creating a git repository*

   Your first step is to create the local repository that will hold your
   code::

       $ mkdir -p ${REPO_DIR}
       $ cd ${REPO_DIR}
       $ git init
       $ git flow init

   The last command will ask you several questions about what you want
   to name your branches; you can just press :kbd:`Enter` for each
   prompt to accept all of the defaults.

   After these commands finish, you will have a working git repository
   in the ``${REPO_DIR}`` directory.  This repository will contain empty
   ``master`` and ``develop`` branches.

2. *Fetch the latest version of the template*

   Next, you fetch the template from its git repository, and merge it
   into your new project's ``develop`` branch::

       $ git remote add template \
             -t c-template \
             git://github.com/redjack/c-template.git
       $ git fetch template
       $ git checkout develop
       $ git merge template/c-template

   At this point, your new project's ``develop`` branch will contain a
   verbatim copy of the latest template.

3. *Update the template with your project's details*

   The template source has several places where it needs to refer to
   directories are values that are based on the name of your project.
   We've used the text ``APPNAME`` as a placeholder to signal all of
   these locations.  You'll need to edit every instance of ``APPNAME``,
   throughout the template, to hold details of your actual project.

   One file deserves special mention:

   *src/APPNAME.pc.in*
      If you're building a library, this is the template pkg-config file
      that you'll use to tell your users how to link with your library.
      Before editing its contents, you'll want to rename this file to
      match your project's name::

          $ git mv src/APPNAME.pc.in src/${PROJECT_SLUG}.pc.in

   You can find a list of all files that you need to modify using the
   ``git grep`` command::

       $ git grep --color -i APPNAME
       .gitignore:APPNAME.pc
       .gitignore:/APPNAME*.tar.gz
       .gitignore:/APPNAME*.tar.bz2
       CMakeLists.txt:set(PROJECT_NAME APPNAME)
       docs/conf.py:project_name = u'APPNAME'
       docs/conf.py:project_slug = u'appname'
       src/APPNAME.pc.in:sphinxdir=${sharedir}/doc/APPNAME/html
       src/APPNAME.pc.in:Name: APPNAME
       src/APPNAME.pc.in:Libs: -L${libdir} -lAPPNAME
       src/CMakeLists.txt:file(GLOB_RECURSE LIBAPPNAME_SRC libAPPNAME/*.c)
       src/CMakeLists.txt:add_library(libAPPNAME SHARED ${LIBAPPNAME_SRC})
       src/CMakeLists.txt:set_target_properties(libAPPNAME PROPERTIES
       src/CMakeLists.txt:    OUTPUT_NAME appname
       src/CMakeLists.txt:target_link_libraries(libAPPNAME
       src/CMakeLists.txt:install(TARGETS libAPPNAME
       src/CMakeLists.txt:configure_file(libAPPNAME.pc.in libAPPNAME.pc)
       src/CMakeLists.txt:install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libAPPNAME.pc
       tests/CMakeLists.txt:    target_link_libraries(${test_name} ${CHECK_LIBRARIES} libAPPNAME)

   The above is an example; you should always run the ``git grep``
   command yourself in case this documentation is not up-to-date with
   respect to the actual contents of the template.

   Most instances of ``APPNAME`` — anything involving a filename or
   directory name, for instance — should be replaced with the value of
   ``${PROJECT_SLUG}``.  Some will be replaced with ``${PROJECT_NAME}``
   instead; it should be obvious from context which is expected.  To
   make the changes, open each of the files listed by ``git grep`` in
   your trusty text editor, make the change, and save the file.

4. *Make an initial release of the project*

   Once you have all of the template files updated with your project's
   details, you'll commit these changes and make an initial release of
   the project.  This release will be version 0.0, and will mark the
   beginning of your actual development.  Note that if you don't have
   GnuPG installed (or dont' have a valid secret key in your keyring),
   substitute the ``-s`` below with ``-a``.

   ::

       $ git add .
       $ git commit -m "Updating template with ${PROJECT_NAME} details"
       $ git flow release start 0.0
       $ git flow release finish -s 0.0
       $ git tag -s 0.0-dev develop

   The final two commands will prompt you for your GnuPG secret key's
   password, since these commands created signed git tags.

   At this point, you have a new, empty project repository, using the
   template, fully updated with your project's details.

5. *Push the changes to your hosting service*

   If this is a public project, we're assuming you're going to host it
   on `GitHub`_.  If it's a private project, we assume you've got a
   private installation of `GitHub Enterprise`_.  It's also perfectly
   fine to use a different code hosting service; just fill in the
   appropriate value for :envvar:`REMOTE_REPO_URL`.

   ::

       $ git remote add origin ${REMOTE_REPO_URL}
       $ git push origin master develop 0.0 0.0-dev

.. _GitHub: https://github.com/
.. _GitHub Enterprise: https://enterprise.github.com/


At this point, you'll have a new, empty project repository both locally,
and pushed out to your remote code hosting service.  Now can you start
developing your new project!
