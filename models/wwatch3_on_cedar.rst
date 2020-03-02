.. Copyright 2018-2020 The MIDOSS project contributors,
.. the University of British Columbia, and Dalhousie University.
..
.. Licensed under a Creative Commons Attribution 4.0 International License
..
..    https://creativecommons.org/licenses/by/4.0/


.. _WWatch3-OnCedar:

**************************************
WAVEWATCH III :sup:`®` on :kbd:`cedar`
**************************************

This section describes the steps to set up and run the Strait of Georgia configuration of the `WAVEWATCH III®`_ model
(wwatch3 hereafter)
on the ComputeCanada `cedar.computecanada.ca`_ HPC cluster.

.. _WAVEWATCH III®: https://polar.ncep.noaa.gov/waves/wavewatch/
.. _cedar.computecanada.ca: https://docs.computecanada.ca/wiki/Cedar


Modules setup
=============

When working on :kbd:`cedar`, the :command:`module load` command must be used to load extra software components.

You can manually load the modules each time you log in,
or you can add the lines to your :file:`$HOME/.bashrc` file so that they are automatically loaded upon login.

The :command:`module load` command required to build wwatch3 is:

.. code-block:: bash

    module load netcdf-fortran-mpi/4.4.4

.. warning::
    The :kbd:`netcdf-fortran-mpi/4.4.4` module is incompatible with the :kbd:`module load netcdf-fortran/4.4.4` module that is required to build MOHID.
    So,
    if you are working on both MOHID and wwatch3,
    you will need to manually load the appropriate modules as necessary.


Create a Workspace and Clone the Configuration Repositories
===========================================================

:kbd:`cedar` provides `several different types of file storage`_.
We use project space for our working environments because it is large,
high performance,
and backed up.
Scratch space is even larger,
also high performance,
but not backed up,
so we use that as the space to execute wwatch3 runs in,
and store their results.
Files more than 60 days old are automatically purged from scratch space on :kbd:`cedar`.

.. _several different types of file storage: https://docs.computecanada.ca/wiki/Storage_and_file_management

:kbd:`cedar` automatically provides environment variables that are more convenient that remembering full paths to access your project and scratch spaces:

* Your project space is at :file:`$PROJECT/$USER/`
* Your scratch space is at :file:`$SCRATCH/`

Create :file:`MIDOSS/` directory trees in your project and scratch spaces:

.. code-block:: bash

    $ mkdir -p $PROJECT/$USER/MIDOSS/wwatch3-5.16
    $ mkdir -p $PROJECT/$USER/MIDOSS/wwatch3-runs
    $ mkdir -p $SCRATCH/MIDOSS/wwatch3

.. note::
    If the above command fails,
    it may be because the symbolic link that :envvar:`PROJECT` points to was not created when your :kbd:`cedar` account was set up.
    Try:

    .. code-block:: bash

        $ cd $HOME
        $ ln -s $HOME/projects/def-allen project

Clone the `SalishSeaWaves`_ repository,
the collection of configuration files for the Strait of Georgia wwatch3 model:

.. _SalishSeaWaves: https://bitbucket.org/salishsea/salishseawaves/src/default/

.. code-block:: bash

    $ cd $PROJECT/$USER/MIDOSS
    $ hg clone ssh://hg@bitbucket.org/salishsea/salishseawaves SalishSeaWaves


Compile WAVEWATCH III :sup:`®`
==============================

Unpack the wwatch3 code tarball and prepare it for building.
*You only need to do this once.*

.. code-block:: bash

    $ cd $PROJECT/$USER/MIDOSS/wwatch3-5.16
    $ tar -xvzf /home/dlatorne/wwatch3.v5.16.tar.gz
    $ ./install_ww3_tar

In answer to the questions from the :kbd:`install_ww3_tar` script:

  * Choose a (L)ocal installation
  * Update your settings to:

    * Choose :kbd:`mpifort` as the Fortran compiler to use
    * Choose :kbd:`mpicc` as the C compiler to use

  * Accept the default answers for other questions

That stage of the build preparation should finish with output that looks similar to::

   ===============================
  --- Set up / update directories ---
   ==============================================================
      Directory /home/dlatorne/project/dlatorne/MIDOSS/test-wwatch3-5.16/work

   Setting up links to comp link and switch ...
   Setting up links to selected GrADS scripts ...
   Setting up links to input files ...
   Install script not a link ...
   Install script identical to tar version, replace by link.


   ===============================
   --- Final remarks ---
   ==============================================================

   To run the WAVEWATCH III executables and the scripts to generate
   and update these executables from arbitrary directories, add the
   following directories to the path of your interactive shell :

        /home/dlatorne/project/dlatorne/MIDOSS/test-wwatch3-5.16/bin
        /home/dlatorne/project/dlatorne/MIDOSS/test-wwatch3-5.16/exe

   Note that 'comp' and 'link' and 'switch' are user/machine specific.

     Several comp and link files for known compilers are found in:
     /home/dlatorne/project/dlatorne/MIDOSS/test-wwatch3-5.16/bin

     If you cannot find one that suits your machine/preferences,
     create custom scripts based on the existing ones and add to bin.


                      ===============================
                    ---       End of program        ---
                      ===============================

Set up the :file:`comp` and :file:`link` scripts in the :file:`bin/` directory:

  * Edit the :file:`comp.Intel` script to change 2 occurrences of :kbd:`mpiifort`
    (with 2 :kbd:`i` s)
    to :kbd:`mpifort`
    (with 1 :kbd:`i`);
    i.e. change:

    .. code-block:: bash
       :linenos:
       :lineno-start: 94

        comp=mpiifort
        which mpiifort 1> /dev/null 2> /dev/null

    to:

    .. code-block:: bash
       :linenos:
       :lineno-start: 94

        comp=mpifort
        which mpifort 1> /dev/null 2> /dev/null

  * Edit the :file:`link.Intel` script to change 2 occurrences of :kbd:`mpiifort`
    (with 2 :kbd:`i` s)
    to :kbd:`mpifort`
    (with 1 :kbd:`i`);
    i.e. change:

    .. code-block:: bash
       :linenos:
       :lineno-start: 109

        comp=mpiifort
        which mpiifort 1> /dev/null 2> /dev/null

    to:

    .. code-block:: bash
       :linenos:
       :lineno-start: 109

        comp=mpifort
        which mpifort 1> /dev/null 2> /dev/null

  * Create symlinks for :file:`comp`,
    :file:`link`,
    and :file:`SalishSeaWaves/switch` in the :file:`bin/` directory:

    .. code-block:: bash

        $ cd bin
        $ ln -sf comp.Intel comp && chmod +x comp.Intel
        $ ln -sf link.Intel link
        $ ln -sf $PROJECT/$USER/MIDOSS/SalishSeaWaves/switch switch

Confirm that the :kbd:`netcdf-fortran-mpi` module is loaded:

.. code-block:: bash

    $ module load netcdf-fortran-mpi/4.4.4

Export the environment variables that are required to build wwatch3:

  .. code-block:: bash

    $ export PATH=$PATH:$PROJECT/$USER/MIDOSS/wwatch3-5.16/bin:$PROJECT/$USER/MIDOSS/wwatch3-5.16/exe
    $ export WWATCH3_NETCDF=NC4
    $ export NETCDF_CONFIG=$(which nc-config)

Compile and link the wwatch3 model programs:

.. code-block:: bash

    $ cd $PROJECT/$USER/MIDOSS/wwatch3-5.16/work
    $ w3_make


.. _GenerateWind&CurrentForcingFilesOnSalish:

Generate Wind & Current Forcing Files on :kbd:`salish`
======================================================

wwatch3 uses netCDF4 wind and current forcing files that are generated from the HRDPS surface forcing files that are used to force SalishSeaCast NEMO runs,
and the surface current fields that are produced by those runs.

For the moment,
generation of those forcing files has to be done on :kbd:`salish` and then the files uploaded from there to :kbd:`cedar`.
This section describes the process for doing that.

We use the :ref:`salishseanowcast:MakeWW3WindFile-Worker` and :ref:`salishseanowcast:MakeWW3CurrentFile-Worker` workers from the SalishSeaCast automation system in :kbd:`--debug` mode to generate the forcing files.

.. warning::
    Always run the workers with the :kbd:`--debug` command-line option.
    That sends all logging information from the workers to the screen instead of log files,
    and prevents the workers from trying to communicate with the automation system manage.

    Running a worker without the :kbd:`--debug` option may disrupt the SalishSeaCast automation system.

Follow the instructions to set up a SalishSeaNowcast :ref:`salishseanowcast:SalishSeaNowcastDevelopmentEnvironment`.

A special SalishSeaNowcast configuration for generating wwatch3 forcing files is stored in :file:`SalishSeaNowcast/config/wwatch3-forcing.yaml`

With your SalishSeaNowcast :ref:`salishseanowcast:SalishSeaNowcastDevelopmentEnvironment` activated,
you can run :ref:`salishseanowcast:MakeWW3WindFile-Worker` with the command:

.. code-block:: bash

    (salishsea-nowcast)$ python -m nowcast.workers.make_ww3_wind_file SalishSeaNowcast/config/wwatch3-forcing.yaml --debug salish nowcast --run-date yyyy-mm-dd

The generated forcing file will be stored in :file:`/data/MIDOSS/forcing/wwatch3/wind/SoG_wind_yyyymmdd.nc`.

Likewise,
you can run :ref:`salishseanowcast:MakeWW3CurrentFile-Worker` with:

.. code-block:: bash

    (salishsea-nowcast)$ python -m nowcast.workers.make_ww3_current_file SalishSeaNowcast/config/wwatch3-forcing.yaml --debug salish nowcast --run-date yyyy-mm-dd

The generated forcing file will be stored in :file:`/data/MIDOSS/forcing/wwatch3/current/SoG_current_yyyymmdd.nc`.

A bash script like:

.. code-block:: bash

    yyyy=2015
    mm=01
    for dd in {01..31}
    do
      python -m nowcast.workers.make_ww3_wind_file SalishSeaNowcast/config/wwatch3-forcing.yaml --debug salish nowcast --run-date ${yyyy}-${mm}-${dd}
      python -m nowcast.workers.make_ww3_current_file SalishSeaNowcast/config/wwatch3-forcing.yaml --debug salish nowcast --run-date ${yyyy}-${mm}-${dd}
    done

will enable you to run the workers for a month at a time.

The generated files are at total of 74M per day
(6.8M for the wind file,
and 67M for the current file).
That scales to approximately 2.2G per month,
26.7G per year,
and 133.3G for the 2015 to late-2019 period covered by the :kbd:`nowcast-green.201812` SalishSeaCast NEMO results dataset.

The files will be produced with :kbd:`-rw-r--r--` permissions.
To make them group-writable,
you can use:

.. code-block:: bash

    find /data/MIDOSS/forcing/wwatch3/ -type f -execdir chmod g+w {} \;

To upload the files to :kbd:`cedar` you can use:

.. code-block:: bash

    rsync -rltv /data/MIDOSS/forcing/wwatch3/ cedar:/scratch/dlatorne/MIDOSS/forcing/wwatch3/
