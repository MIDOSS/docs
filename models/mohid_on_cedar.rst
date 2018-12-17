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

    mkdir -p $PROJECT/$USER/MIDOSS/results

.. note::
    If the above command fails,
    it may be because the symbolic link that :envvar:`PROJECT` points to was not created when your :kbd:`cedar` account was set up.
    Try:

    .. code-block:: bash

        cd $HOME
        ln -s $HOME/projects/def-allen project

Clone the following repositories:

* `moad_tools`_ that provides the :command:`hdf5-to-netcdf4` tool for by `MOHID-Cmd`_
  (or at the command-line, if necessary)
* `MOHID-Cmd`_,
  the MIDOSS-MOHID command processor for setting up and managing `MIDOSS-MOHID` _code runs
* `MIDOSS-MOHID`_,
  the MIDOSS project version of `MOHID`_ that includes developed at Dalhousie University

.. _moad_tools: https://bitbucket.org/UBC_MOAD/moad_tools/
.. _MOHID-Cmd: https://bitbucket.org/midoss/mohid-cmd/
.. _MIDOSS-MOHID : https://bitbucket.org/midoss/midoss-mohid/

.. code-block:: bash

    cd $PROJECT/$USER/MIDOSS
    hg clone ssh://hg@bitbucket.org/UBC_MOAD/moad_tools
    hg clone ssh://hg@bitbucket.org/midoss/mohid-cmd MOHID-Cmd
    hg clone ssh://hg@bitbucket.org/midoss/midoss-mohid MIDOSS-MOHID

or

.. code-block:: bash

    cd $PROJECT/$USER/MIDOSS
    hg clone https://your_userid@bitbucket.org/UBC_MOAD/moad_tools
    hg clone https://your_userid@bitbucket.org/midoss/mohid-cmd MOHID-Cmd
    hg clone https://your_userid@bitbucket.org/midoss/midoss-mohid MIDOSS-MOHID

if you don't have `ssh key authentication`_ set up on Bitbucket
(replace :kbd:`you_userid` with you Bitbucket userid,
or copy the link from the :guilabel:`Clone` action pop-up on the repository`page).

.. _ssh key authentication: https://confluence.atlassian.com/bitbucket/set-up-an-ssh-key-728138079.html


Install :kbd:`moad_tools` and :kbd:`MOHID-Cmd`
----------------------------------------------

.. note::
    This method of installing the :kbd:`moad_tools` and :kbd:`MOHID-Cmd` Python packages employs the `"user scheme" for installation`_.
    It is appropriate and necessary on :kbd:`cedar` where we *do not* have our own `Anaconda Python`_ distribution installed.
    This method *should not* be used EOAS work stations or other machines where you have `Anaconda Python`_ installed.

    .. _"user scheme" for installation: https://packaging.python.org/tutorials/installing-packages/#installing-to-the-user-site
    .. _Anaconda Python: https://www.anaconda.com/what-is-anaconda/

.. code-block:: bash

    cd $PROJECT/$USER/MIDOSS
    pip install --user moad_tools
    pip install --user MOHID-Cmd

You can confirm that :kbd:`moad_tools` and :command:`hdf5-to-netcdf4` are correctly installed with the command:

.. code-block:: bash

    hdf5-to-netcdf4 --help

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

    mohid --help

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

    salloc --time=00:10:0 --cpus-per-task=1 --mem-per-cpu=20000m --account=rrg-allen

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

    hdf5-to-netcdf4 Lagrangian.hdf5 Lagrangian.nc

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

.. code-block:: bash

    $ cd $PROJECT/$USER/MIDOSS/MOHID/Solutions/mohid-in-linux
    $ ./compile_mohid.sh -mb1 -mb2 -mw

The output looks something like::

  #### Mohid Base 1 ####
   compile mohidbase1 OK


  #### Mohid Base 2 ####
   compile mohidbase2 OK


  #### Mohid Water ####
   compile MohidWater OK

  ==========================================================================
  build started:    Thu Sep 27 16:07:56 PDT 2018
  build completed:  Thu Sep 27 16:14:38 PDT 2018

  --->                  Executables ready                               <---

  total 8
  lrwxrwxrwx 1 dlatorne def-allen 36 Sep 27 16:14 MohidWater.exe -> ../src/MohidWater/bin/MohidWater.exe

  ==========================================================================

You can delete all of the compiled objects,
libraries,
and executables with:

.. code-block:: bash

    $ ./compile_mohid --clean

so that the next build will be "clean";
i.e. it won't be able to include any products from previous builds.

.. note::
    If you going to make changes to the MOHID code,
    you almost certainly should fork the `MOHID repo`_ on GitHub and change the :command:`git` :kbd:`remotes` so that the project repo is named :kbd:`upstream` and your fork is named :kbd:`origin`.
    That will enable you to use a version control workflow,
    committing your changes,
    and pushing them to GitHub for sharing and redundant storage.

    .. _MOHID repo: https://github.com/Mohid-Water-Modelling-System/Mohid

Compilation can up (≥15%) faster in an `interactive job`_ on :kbd:`cedar`.
Be sure to request at least 1024 MB of memory:

.. _interactive job: https://docs.computecanada.ca/wiki/Running_jobs#Interactive_jobs

.. code-block:: bash

    salloc --time=0:10:0 --cpus-per-task=1 --mem-per-cpu=1024m --account=rrg-allen


Test MOHID
----------

The `MOHID repo`_ includes a very lightweight test case that you can run to confirm that your built executable works:

.. warning::
    Please *do not* execute MOHID runs more complex than this test case on the :kbd:`cedar` login node.
    MOHID starts threads on all available processors.
    Doing that for more than a few seconds will make you very unpopular with other :kbd:`cedar` users and the system administrators.

.. code-block:: bash

    $ cd $PROJECT/$USER/MIDOSS/MOHID/Solutions/mohid-in-linux/test/mohidwater/25m_deep/exe/
    $ ./MohidWater.exe

The output looks something like::

  -------------------------- MOHID -------------------------

        AUTHOR   : IST/MARETEC, Marine Modelling Group
        WWW      : http://www.mohid.com


  Copyright (C) 1985, 1998, 2002, 2006.
  Instituto Superior Tecnico, Technical University of Lisbon
  -------------------------- MOHID -------------------------

  Constructing Mohid Water
  Please Wait...
  -------------------------- MODEL -------------------------

  Constructing      :
  ID                :            1

  OPENMP: Max number of threads available is           32
  OPENMP: Using the max number of threads available
  --------------------- WATERPROPERTIES --------------------

  Num of Properties :            0

  ---------------- INTERFACE SEDIMENT-WATER -----------------

  Num of Properties :            0


  Atmosphere DT Prediction Method:            1

  -------------------------- MOHID -------------------------

  Running MOHID, please wait...

  -----Current Simulation Instant---------------------------
  Time Instant           : 2009: 3: 1: 7:34: 0

  -----CPU Time---------------------------------------------
  Elapsed                :            2s
  Remaining (aprox.)     :            5s
  Completed (%)          :        31.5278
  Coeficient CPU / Model :         0.0001
  Seconds per Iteration  :         0.0101s
  -----System Time------------------------------------------
  System time            : 2018: 9:27:16:30:47
  End of the run         : 2018: 9:27:16:30:53



  -----Current Simulation Instant---------------------------
  Time Instant           : 2009: 3: 1:16:20: 0

  -----CPU Time---------------------------------------------
  Elapsed                :            4s
  Remaining (aprox.)     :            2s
  Completed (%)          :        68.0556
  Coeficient CPU / Model :         0.0001
  Seconds per Iteration  :         0.0089s
  -----System Time------------------------------------------
  System time            : 2018: 9:27:16:30:50
  End of the run         : 2018: 9:27:16:30:52



  -------------------------- MOHID -------------------------

  Shuting down MOHID, please wait...

  -----Current Simulation Instant---------------------------
  Time Instant           : 2009: 3: 2: 0: 0: 0

  -----CPU Time---------------------------------------------
  Elapsed                :            6s
  Remaining (aprox.)     :            0s
  Completed (%)          :       100.0000
  Coeficient CPU / Model :         0.0001
  Seconds per Iteration  :         0.0098s
  -----System Time------------------------------------------
  System time            : 2018: 9:27:16:30:52
  End of the run         : 2018: 9:27:16:30:52



  -------------------------- MOHID -------------------------

  Program Mohid Water successfully terminated


  Total Elapsed Time     :           7.67   0h  0min  7s

  Total CPU time         :         190.81

  CPU usage (%)          :        2488.74

  Workcycle Elapsed Time :           6.94

  Workcycle CPU time     :         178.22

  Workcycle CPU usage (%):        2569.55


  ----------------------------------------------------------

The output consists of 2 files in the :file:`exe/` directory::

  $ ls -l
  -rw-rw-r-- 1 dlatorne def-allen 48450 Sep 27 16:30 UsedKeyWords_1.dat
  -rw-rw-r-- 1 dlatorne def-allen  4660 Sep 27 16:30 Error_and_Messages_1.log

and 2 files in the :file:`res/` directory::

  $ ls -l ../res/
  -rw-rw-r-- 1 dlatorne def-allen   3827 Sep 27 16:30 Outwatch_1.txt
  -rw-rw-r-- 1 dlatorne def-allen 779688 Sep 27 16:30 Hydrodynamic_1.hdf5
