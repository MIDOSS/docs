.. Copyright 2018 The MIDOSS project contributors,
.. the University of British Columbia, and Dalhousie University.
..
.. Licensed under a Creative Commons Attribution 4.0 International License
..
..    https://creativecommons.org/licenses/by/4.0/


.. _MOHID-OnCedar:

****************************
MIDOSS MOHID on :kbd:`cedar`
****************************

This section describes the steps to set up and run the MIDOSS version of the `MOHID`_ code on the ComputeCanada `cedar.computecanada.ca`_ HPC cluster.

.. _MOHID: http://www.mohid.com/
.. _cedar.computecanada.ca: https://docs.computecanada.ca/wiki/Cedar


Modules setup
=============

When working on :kbd:`cedar`, the :command:`module load` command must be used to load extra software components.

You can manually load the modules each time you log in,
or you can add the lines to your :file:`$HOME/.bashrc` file so that they are automatically loaded upon login.

The :command:`module load` commands needed are:

.. code-block:: bash

    module load nco/4.6.6
    module load netcdf-fortran/4.4.4
    module load proj4-fortran/1.0
    module load python/3.7.0

.. warning::
    The :kbd:`nco/4.6.6` module is incompatible with the :kbd:`module load netcdf-fortran-mpi/4.4.4` module that is required to run NEMO.
    So,
    if you are running both MIDOSS-MOHID and NEMO,
    you will need to manually load the appropriate modules as necessary.


Create a Workspace and Clone the Tools and Code Repositories
============================================================

:kbd:`cedar` provides`several different types of file storage`_.
We use project space for our working environments because it is large,
high performance,
and backed up.
Scratch space is even larger,
also high performance,
but not backed up,
so we use that as the space to execute MOHID runs in,
but generally move the run results to project space.
Files more than 60 days old are automatically purged from scratch space on :kbd:`cedar`.

.. _several different types of file storage: https://docs.computecanada.ca/wiki/Storage_and_file_management

:kbd:`cedar` automatically provides environment variables that are more convenient that remembering full paths to access your project and scratch spaces:

* Your project space is at :file:`$PROJECT/$USER/`
* Your scratch space is at :file:`$SCRATCH/`

Create a :file:`MIDOSS/` directory tree in your project space:

.. code-block:: bash

    $ mkdir -p $PROJECT/$USER/MIDOSS/results

.. note::
    If the above command fails,
    it may be because the symbolic link that :envvar:`PROJECT` points to was not created when your :kbd:`cedar` account was set up.
    Try:

    .. code-block:: bash

        $ cd $HOME
        $ ln -s $HOME/projects/def-allen project

Clone the following repositories:

* `moad_tools`_ that provides the :command:`hdf5-to-netcdf4` tool for by `MOHID-Cmd`_
  (or at the command-line, if necessary)
* `MOHID-Cmd`_,
  the MIDOSS-MOHID command processor for setting up and managing `MIDOSS-MOHID` _code runs
* `NEMO-Cmd`_, the NEMO command processor that MOHID-Cmd uses code from
* `MIDOSS-MOHID`_,
  the MIDOSS project version of `MOHID`_ that includes developed at Dalhousie University

.. _moad_tools: https://bitbucket.org/UBC_MOAD/moad_tools/
.. _MOHID-Cmd: https://bitbucket.org/midoss/mohid-cmd/
.. _NEMO-Cmd:
.. _MIDOSS-MOHID : https://bitbucket.org/midoss/midoss-mohid/

.. code-block:: bash

    $ cd $PROJECT/$USER/MIDOSS
    $ hg clone ssh://hg@bitbucket.org/UBC_MOAD/moad_tools
    $ hg clone ssh://hg@bitbucket.org/midoss/mohid-cmd MOHID-Cmd
    $ hg clone ssh://hg@bitbucket.org/salishsea/nemo-cmd NEMO-Cmd
    $ hg clone ssh://hg@bitbucket.org/midoss/midoss-mohid MIDOSS-MOHID


Install :kbd:`moad_tools` and :kbd:`MOHID-Cmd`
----------------------------------------------

.. note::
    This method of installing the :kbd:`moad_tools`, :kbd:`NEMO-Cmd`, and :kbd:`MOHID-Cmd` Python packages employs the `"user scheme" for installation`_.
    It is appropriate and necessary on :kbd:`cedar` where we *do not* have our own `Anaconda Python`_ distribution installed.
    This method *should not* be used on EOAS work stations or other machines where you have `Anaconda Python`_ installed.

    .. _"user scheme" for installation: https://packaging.python.org/tutorials/installing-packages/#installing-to-the-user-site
    .. _Anaconda Python: https://www.anaconda.com/what-is-anaconda/

.. code-block:: bash

    $ cd $PROJECT/$USER/MIDOSS
    $ pip install --user moad_tools
    $ pip install --user NEMO-Cmd
    $ pip install --user MOHID-Cmd

You can confirm that :kbd:`moad_tools` and :command:`hdf5-to-netcdf4` are correctly installed with the command:

.. code-block:: bash

    $ hdf5-to-netcdf4 --help

from which you should see output like::

  Usage: hdf5-to-netcdf4 [OPTIONS] HDF5_FILE NETCDF4_FILE

    Transform selected contents of a MOHID HDF5 results file HDF5_FILE into a
    netCDF4 file stored as NETCDF4_FILE.

  Options:
    -v, --verbosity [debug|info|warning|error|critical]
                                    Choose how much information you want to see
                                    about the progress of the transformation;
                                    warning, error, and critical should be
                                    silent unless something bad goes wrong.
                                    [default: warning]
    --help                          Show this message and exit.

You can confirm that :kbd:`MOHID-Cmd` is correctly installed with the command:

.. code-block:: bash

    $ mohid --help

from which you should see output like::

  usage: mohid [--version] [-v | -q] [--log-file LOG_FILE] [-h] [--debug]

  MIDOSS-MOHID Command Processor

  optional arguments:
    --version            show program's version number and exit
    -v, --verbose        Increase verbosity of output. Can be repeated.
    -q, --quiet          Suppress output except warnings and errors.
    --log-file LOG_FILE  Specify a file to log output. Disabled by default.
    -h, --help           Show help message and exit.
    --debug              Show tracebacks on errors.

  Commands:
    complete       print bash completion command (cliff)
    help           print detailed help for another command (cliff)
    prepare        Set up the MIDOSS-MOHID run described in DESC_FILE and print the path of the temporary run directory.


Using :command:`hdf5-to-netcdf4`
--------------------------------

.. note::
    `MOHID-Cmd`_ is not yet fully functional.
    In particular,
    it does not yet include the ability to transform MOHID HDF5 output files into netCDF4 files.
    For now you have to do that using the :command:`hdf5-to-netcdf4` command-line tool.

The :command:`hdf5-to-netcdf4` command-line tool can be used to transform a MOHID HDF5 output file into a netCDF4 file.
Doing so is resource intensive in terms of memory and disk i/o,
so it has to be done in an `interactive slurm session`_ on :kbd:`cedar`.

.. _interactive slurm session: https://docs.computecanada.ca/wiki/Running_jobs#Interactive_jobs

Start an interactive :kbd:`slurm` session with a command like:

.. code-block:: bash

    $ salloc --time=00:10:0 --cpus-per-task=1 --mem-per-cpu=20000m --account=rrg-allen

Choose the :kbd:`--time` value to be close to what you expect to need in order to avoid having to wait too long for the session to be allocated to you.
For guidance,
transformation of a :file:`Lagrangian.hdf5` from a MOHID run for 7 days of model time on the SalishSeaCast domain takes about 6m30s.

.. note::
    At present,
    running :command:`hdf5-to-netcdf4` on cedar requires a crazy large memory allocation,
    nearly 20,000 Mb.
    It is hoped that will change in the future.

One the interactive session starts,
do the transformation with:

.. code-block:: bash

    $ hdf5-to-netcdf4 Lagrangian.hdf5 Lagrangian.nc

You can prefix the hdf5 and nc file names with paths.
You can get progress information from :command:`hdf5-to-netcdf4` by using the options :kbd:`--verbosity info` or :kbd:`--verbosity debug`.
Please see :command:`hdf5-to-netcdf4 --help` for details.


Compile MOHID
=============

Compile and link the `Mohid_Base_1`_,
`Mohid_Base_2`_,
and `MohidWater`_ parts of the `MOHID Framework`_ with the commands:

.. _Mohid_Base_1: http://wiki.mohid.com/index.php?title=Mohid_Base_1
.. _Mohid_Base_2: http://wiki.mohid.com/index.php?title=Mohid_Base_2
.. _MohidWater: http://wiki.mohid.com/index.php?title=Mohid_Water
.. _MOHID Framework: http://wiki.mohid.com/index.php?title=Mohid_Framework

Use an `interactive job`_ on :kbd:`cedar` for compilation because it is substantially faster (≥15%).
Be sure to request at least 1024 MB of memory:

.. _interactive job: https://docs.computecanada.ca/wiki/Running_jobs#Interactive_jobs

.. code-block:: bash

    $ salloc --time=0:10:0 --cpus-per-task=1 --mem-per-cpu=1024m --account=rrg-allen
    $ cd $PROJECT/$USER/MIDOSS/MIDOSS-MOHID/Solutions/mohid-in-linux
    $ ./compile_mohid.sh -mb1 -mb2 -mw

The output looks something like::

  #### Mohid Base 1 ####
   compile mohidbase1 OK


  #### Mohid Base 2 ####
   compile mohidbase2 OK


  #### Mohid Water ####
   compile MohidWater OK

  ==========================================================================
  build started:    Tue Dec 18 13:10:09 PST 2018
  build completed:  Tue Dec 18 13:16:07 PST 2018

  --->                  Executables ready                               <---

  total 0
  lrwxrwxrwx 1 dlatorne def-allen 36 Dec 18 13:16 MohidWater.exe -> ../src/MohidWater/bin/MohidWater.exe

  ==========================================================================

You can delete all of the compiled objects,
libraries,
and executables with:

.. code-block:: bash

    $ ./compile_mohid --clean

so that the next build will be "clean";
i.e. it won't be able to include any products from previous builds.
