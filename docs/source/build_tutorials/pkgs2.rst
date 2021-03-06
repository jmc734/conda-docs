====================================
Building conda packages from scratch
====================================

This second tutorial is about building a conda package without using the ``conda skeleton pypi``
command. In this tutorial we will build the same recipe as in the first tutorial, Pyinstrument, but
this time we will find a similar recipe and edit it to make a new recipe. We also write the recipe
completely from scratch. We also explore the elements of a conda package in more detail.

You should have already completed :doc:`pkgs`.

Building conda packages summary
===============================

This tutorial includes the following:

#. :ref:`before-you-start`
#. :ref:`what-is-a-conda-package`
#. :ref:`about-conda-build-recipe`
#. :ref:`edit-similar-recipe`
#. :ref:`edit-meta-yaml`
#. :ref:`build-sh-bld-bat`
#. :ref:`build-and-install`
#. :ref:`convert`
#. :ref:`anaconda-org`
#. :ref:`more-resources`


.. _before-you-start:

Before you start
----------------

You should already have conda and conda-build by downloading and installing Miniconda or Anaconda.
Conda-build is not automatically installed, so let's do that now.

All users install conda-build:

.. code-block:: bash

    conda install conda-build

Now you are ready to start building your own conda package.

.. _what-is-a-conda-package:

What is a conda package?
------------------------

A conda package is a package that can be installed using the ``conda install [packagename]`` command.

It includes a link to a tarball or bzipped tar archive (.tar.bz2) which contains metadata under the
``info/`` directory, and a collection of files which are installed directly into an ``install`` prefix.

The format is identical across platforms and operating systems.  During the install process,
files are extracted into the install prefix, except for files in the ``info/``
directory. Installing the files of a conda package into an environment can be thought of as changing the
directory to an environment, then downloading and extracting the zip file and its dependencies -- all with
the single ``conda install [packagename]`` command.

.. _about-conda-build-recipe:

About conda build recipe
------------------------

Building a package requires a recipe. A conda build recipe is flat directory which contains the following files:

* ``meta.yaml`` contains all the metadata in the recipe. Only package/name and package/version are required fields.

* ``build.sh``  The Unix script that installs the files for the package on OS X and Linux, and is executed using the bash command.

* ``bld.bat``  The build script that installs the files for the package on Windows, and is executed using cmd.

* ``run_test.py`` Optional Python test file, a test script that will run automatically if it is part of the recipe.

* Optional: patches that will be applied to the source.

* Other resources, which are not included in the source and cannot be generated by the build scripts.

NOTE: When you use the conda skeleton command, the first three files (``meta.yaml``, ``build.sh`` and ``bld.bat``)
are automatically generated for you. See the previous tutorial :doc:`pkgs` for
more information.

.. _edit-similar-recipe:

Edit a similar conda recipe
---------------------------

Now we will create a recipe for the same package that we did in the first tutorial, Pyinstrument. But this
time we will find a similar recipe from from the conda-recipes repo on GitHub, and manually edit  the values
in the  meta.yaml file.

We will edit a copy of the meta.yaml used in a program named Pyfaker. Despite its name, Pyfaker is a real
Python package that generates fake data for testing. We will edit this file into the meta.yaml file we want
for our new Pyinstrument package.

Let’s start by making a new directory for this tutorial named pyinstrument, and change to the new directory.

All users:

.. code-block:: bash

  mkdir pyinstrument
  cd pyinstrument

Now, let’s go get a copy of the Pyfaker’s meta.yaml for us to edit.

In your browser, go to  https://github.com/conda/conda-recipes/ .

In the top search box, search for the package named "pyfaker." In the search results, you will see a link to
the ``pyfaker/meta.yaml`` file. Open that file.

To save this small file locally, copy its contents, open your favorite text editor, open a new file, and paste its
contents.

Save this new file as ``meta-pyfaker.yaml``

Next, remove and replace the Pyfaker information with the 6 fields below.

The fields we want to replace in our pyinstrument script are:

+--------------+---------------------------------------------------------------------------+
| Name         | pyinstrument                                                              |
+--------------+---------------------------------------------------------------------------+
| Git tag      | v0.13.1 (or latest from https://github.com/joerick/pyinstrument/releases) |
+--------------+---------------------------------------------------------------------------+
| Git URL      | https://github.com/joerick/pyinstrument.git                               |
+--------------+---------------------------------------------------------------------------+
| Imports      | pyinstrument                                                              |
+--------------+---------------------------------------------------------------------------+
| Home         | https://github.com/joerick/pyinstrument                                   |
+--------------+---------------------------------------------------------------------------+
| License      | BSD                                                                       |
+--------------+---------------------------------------------------------------------------+
| License file | LICENSE                                                                   |
+--------------+---------------------------------------------------------------------------+

When finished, save to the same directory, this time with the correct name, meta.yaml.
When you are finished, compare your meta.yaml with the answer below:

.. literalinclude:: meta.yaml

How to use PyPI as the source instead of GitHub
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

What if you wanted to use PyPI or another repository instead of GitHub? There is little difference to conda-build
between building from Git versus building from a tarball on a repository like PyPI. Because the same source
is hosted on PyPI and GitHub, you can easily find a script on  PyPI instead of GitHub. Simply replace this
“source” section:

.. code-block:: bash

    git_rev: 0.12
    git_url: https://github.com/joerick/pyinstrument.git

With the following:

.. code-block:: bash

    fn: pyinstrument-0.13.1.tar.gz
    md5: e347036acc50720c0903dc2221b2605d
    url: https://pypi.python.org/packages/source/p/pyinstrument/pyinstrument-0.13.1.tar.gz

NOTE: The md5 is found on the `PyPI Pyinstrument page <https://pypi.python.org/pypi/pyinstrument>`_.

More info about the meta.yaml file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

See more information about all the possible values that can go in the meta.yaml file on the
:doc:`../build`  page.

.. _edit-meta-yaml:

Manually edit the file meta.yaml
--------------------------------

This time instead of removing and replacing text, we will manually create a new ``meta.yaml`` file. If you
want to keep your file from Step 4 above so you can look at it later, rename it something like ``meta-step4.yaml.``
Later when you are finished, you can rename the file meta.yaml.

To create a new meta.yaml file, open your favorite editor. Create a new text file and insert the following
information. A blank sample meta.yaml follows to make it easier to match up the information.

+--------------+---------------------------------------------+
| Name         | pyinstrument                                |
+--------------+---------------------------------------------+
| Git rev      | 0.13                                        |
+--------------+---------------------------------------------+
| Git URL      | https://github.com/joerick/pyinstrument.git |
+--------------+---------------------------------------------+
| Imports      | pyinstrument                                |
+--------------+---------------------------------------------+
| Home         | https://github.com/joerick/pyinstrument     |
+--------------+---------------------------------------------+
| License      | BSD                                         |
+--------------+---------------------------------------------+
| License file | LICENSE                                     |
+--------------+---------------------------------------------+

.. code-block:: bash

  package:
    name:

  source:
    git_rev:
    git_url:

  requirements:
    build:
      - python
      - setuptools

    run:
      - python

  test:
    imports:
      -

  about:
    home:
    license:
    license_file:

When you are finished, save the file in the same pyinstrument directory as ``meta.yaml``. Check your work against
the results in Section 4 above.

.. _build-sh-bld-bat:

Write the build script files build.sh and bld.bat
-------------------------------------------------

The other two files you need for a build are

* **build.sh** shell script for Linux and OS X, and
* **bld.bat** batch file for Windows.

These two build files contain all the variables such as for 32-bit or 64-bit architecture (the ARCH
variable) and the build environment prefix (PREFIX). The two files ``build.sh`` and ``bld.bat`` files must be
in the same directory as your ``meta.yaml`` file.

First we'll write the build file for Linux and OS X and then the build file for Windows.

All users, in your favorite text editor, create a new file named ``build.sh`` and enter the text exactly as
shown:

.. code-block:: bash

    $PYTHON setup.py install     # Python command to install the script.

That's it! Save your new build.sh file to the same directory where you put the meta.yaml file.

Now we’ll write the Windows build script file. All users, still in your favorite text editor, create another
new file named bld.bat and enter the text exactly as shown:

.. code-block:: bash

    "%PYTHON%" setup.py install
    if errorlevel 1 exit 1

bld.bat must be formatted differently, and needs the command that tells it to
exit if it encounters an error.

NOTE: In bld.bat, the best practice is to to add the ``if errorlevel 1 exit 1`` after every command so if the commnand fails,
the build fails.

Save this new file ``bld.bat`` to the same directory where you put your new ``meta.yaml`` and ``build.sh`` files.

More information on environment variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For more information on build files, see :doc:`../build`.

.. _build-and-install:

Build and install
-----------------

Now that you have your three new build files ready, you are ready to create your new package with
``conda build``, and install the package on your local computer.

Run conda build:

.. code-block:: bash

    conda build pyinstrument

When conda-build is finished, it displays the filename and the file's location at the end of the build.
In our case the file was saved to:

.. code-block:: bash

    ~/anaconda/conda-bld/linux-64/pyinstrument-0.13.1-py27_0.tar.bz2

NOTE: Save this path and file information for the next step. The exact path and filename will vary depending
on your operating system, and whether you are using Anaconda or Miniconda. Conda-build tells you the exact
location and filename.

Now install your newly built program on your local computer by using the use-local flag:

.. code-block:: bash

    conda install --use-local pyinstrument

We know that Pyinstrument installed successfully if there are no error messages.

.. _convert:

Convert package for use on all platforms
----------------------------------------

Now that you have built a package for your current platform with conda-build, you can convert it for use on
other platforms. This is why you made the two build files, ``build.sh`` and ``bld.bat`` for all platforms.

Use the conda convert command with a platform specifier from the list
{osx-64,linux-32,linux-64,win-32,win-64,all}. We will use the platform specifier all, as shown:

.. code-block:: bash

    conda convert --platform all ~/anaconda/conda-bld/linux-64/pyinstrument-0.13.1-py27_0.tar.bz2 -o outputdir/

NOTE: change your path and filename to the exact path and filename you saved in Step 7.

.. _anaconda-org:

Optional: Upload new packages to Anaconda.org
---------------------------------------------

After converting your files for use on other platforms, you may choose to upload your files to Anaconda.org, formerly known as binstar.org.
It only takes a minute to do if you have a free Anaconda.org account.

If you haven’t already, open a free Anaconda.org account and record your new username and password.

Next, in your terminal window, run ``conda install anaconda-client`` and enter your new Anaconda.org username and password.

Again in your terminal window, log into your Anaconda.org account with the command:

.. code-block:: bash

    anaconda login

And upload your package to Anaconda.org:

.. code-block:: bash

    anaconda upload ~/miniconda/conda-bld/linux-64/pyinstrument-0.12-py27_0.tar.bz

NOTE: Change your path and filename to the exact path and filename you saved in Step 7.

TIP: To save time, you can set conda to always automatically upload a successful build to Anaconda.org
with the command: ``conda config --set anaconda_upload yes``

.. _more-resources:

More resources
--------------

:doc:`../build`

:doc:`../commands`
