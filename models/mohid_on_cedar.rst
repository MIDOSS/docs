.. Copyright 2018 The MIDOSS project contributors,
.. the University of British Columbia, and Dalhousie University.
..
.. Licensed under a Creative Commons Attribution 4.0 International License
..
..    https://creativecommons.org/licenses/by/4.0/


.. _MOHID-OnCedar:

*********************
MOHID on :kbd:`cedar`
*********************

This section describes the steps to set up and run the SalishSeaCast `MOHID`_ version 18.06+ code on the ComputeCanada machines `graham.computecanada.ca`_ and `cedar.computecanada.ca`_.

.. _MOHID: http://www.mohid.com/
.. _graham.computecanada.ca: https://docs.computecanada.ca/wiki/Graham
.. _cedar.computecanada.ca: https://docs.computecanada.ca/wiki/Cedar


Modules setup
=============

When working on the ComputeCanada clusters, the :command:`module load` command must be used to load extra software components.

You can manually load the modules each time you log in,
or you can add the lines to your :file:`$HOME/.bashrc` file so that they are automatically loaded upon login.

At present, :kbd:`cedar` and :kbd:`graham` are configured similarly.
The modules needed are:

.. code-block:: bash

    module load python/3.7.0
    module load netcdf-fortran-mpi/4.4.4
    module load proj4-fortran/1.0


Create a Workspace and Clone the MOHID Code Repository
======================================================

:kbd:`cedar` and :kbd:`graham` provide `several different types of file storage`_.
We use project space for our working environments because it is large,
high performance,
and backed up.
Scratch space is even larger,
also high performance,
but not backed up,
so we use that as the space to execute MOHID runs in,
but generally move the run results to project space.

Both systems provide environment variables that are more convenient that remembering full paths to access your project and scratch spaces:

* Your project space is at :file:`$PROJECT/$USER/`
* Your scratch space is at :file:`$SCRATCH/`
* Daily atmospheric,
  river,
  and boundary forcing files are in the :file:`$PROJECT/SalishSea/forcing/` tree

.. _several different types of file storage: https://docs.computecanada.ca/wiki/Storage_and_file_management

Create a :file:`MIDOSS/` directory tree in your project space:

.. code-block:: bash

    mkdir -p $PROJECT/$USER/MIDOSS/results

Clone the MOHID code repo:

.. code-block:: bash

    cd $PROJECT/$USER/MIDOSS
    git clone https://github.com/Mohid-Water-Modelling-System/Mohid.git MOHID


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
