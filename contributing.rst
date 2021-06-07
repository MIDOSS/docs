.. Copyright 2018-2021 The MIDOSS project contributors,
.. the University of British Columbia, and Dalhousie University.
..
.. Licensed under a Creative Commons Attribution 4.0 International License
..
..    https://creativecommons.org/licenses/by/4.0/


.. _MIDOSS-DocsContributing:

**************************
Contributing to These Docs
**************************

.. image:: https://img.shields.io/badge/license-CC--BY-lightgrey.svg
    :target: https://creativecommons.org/licenses/by/4.0/
    :alt: Licensed under the Creative Commons Attribution 4.0 International License
.. image:: https://img.shields.io/badge/version%20control-git-blue.svg?logo=github
    :target: https://github.com/MIDOSS/docs
    :alt: Git on GitHub
.. image:: https://readthedocs.org/projects/midoss-docs/badge/?version=latest
    :target: https://midoss-docs.readthedocs.io/en/latest/
    :alt: Documentation Status
.. image:: https://github.com/MIDOSS/docs/workflows/sphinx-linkcheck/badge.svg
    :target: https://github.com/MIDOSS/docs/actions?query=workflow%3Asphinx-linkcheck
    :alt: Sphinx linkcheck
.. image:: https://img.shields.io/github/issues/MIDOSS/docs?logo=github
    :target: https://github.com/MIDOSS/docs/issues
    :alt: Issue Tracker
.. image:: https://img.shields.io/badge/python-3.6+-blue.svg
    :target: https://docs.python.org/3.8/
    :alt: Python Version

Additions,
improvements,
and corrections to these docs are *always* welcome.

The quickest way to fix typos, etc. on existing pages is to use the :guilabel:`Edit on GitHub` link in the upper right corner of the page to get to the online editor for the page on `GitHub`_.

For more substantial work,
and to add new pages,
the instructions below explain how to:

* clone the repository from `GitHub`_

* set up a conda environment in which you can build the docs locally instead of having to push commits to GitHub to trigger a `build on readthedocs.org`_

* build the docs with your changes,
  and preview them in Firefox

.. _GitHub: https://github.com/MIDOSS/docs
.. _build on readthedocs.org: https://readthedocs.org/projects/midoss-docs/builds/


.. _MIDOSS-DocsGettingTheRepo:

Getting the Repo
================

.. image:: https://img.shields.io/badge/version%20control-git-blue.svg?logo=github
    :target: https://github.com/MIDOSS/docs
    :alt: Git on GitHub

Clone the MIDOSS documentation `repository`_ from GitHub with:

.. _repository: https://github.com/MIDOSS/docs

.. code-block:: bash

    $ git clone git@github.com:MIDOSS/docs.git

or copy the URI
(the stuff after :kbd:`git clone` above)
from the :guilabel:`Code` button on the `repository`_ page.

.. note::

    The :kbd:`git clone` command above assumes that your are `connecting to GitHub using SSH`_.
    If it fails,
    please follow the instructions in our :ref:`SecureRemoteAccess` docs to set up your SSH keys and :ref:`CopyYourPublicSshKeyToGitHub`.

    .. _connecting to GitHub using SSH: https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh


.. _MIDOSS-DocsBuildEnvironment:

Docs Build Environment
======================

.. image:: https://img.shields.io/badge/python-3.6+-blue.svg
    :target: https://docs.python.org/3.8/
    :alt: Python Version

Setting up an isolated docs build environment using `Conda`_ is recommended.
Assuming that you have the `Miniconda3`_ installed,
you can create and activate an environment called :kbd:`midoss-docs` that will have all of the Python packages necessary for building the documentation with the commands:

.. _Conda: https://conda.io/en/latest/
.. _Miniconda3: https://docs.conda.io/en/latest/miniconda.html

.. code-block:: bash

    $ cd docs
    $ conda env create -f environment.yaml
    $ conda activate midoss-docs

To deactivate the environment use:

.. code-block:: bash

    (midoss-docs)$ conda deactivate

.. note::
    If you are using a version of :command:`conda` older than 4.4.0,
    the commands to activate and deactivate the environment are:

    .. code-block:: bash

        $ source activate midoss-docs

    and

    .. code-block:: bash

        (midoss-docs)$ source deactivate

    You can check what version of :command:`conda` you are using with :command:`conda --version`.


.. _MIDOSS-DocsBuildingAndPreviewingTheDocumentation:

Building and Previewing the Documentation
=========================================

.. image:: https://readthedocs.org/projects/midoss-docs/badge/?version=latest
    :target: https://midoss-docs.readthedocs.io/en/latest/
    :alt: Documentation Status

The MIDOSS documentation is written in `reStructuredText`_ and converted to HTML using `Sphinx`_.
Creating a :ref:`MIDOSS-DocsBuildEnvironment` as described above includes the installation of Sphinx.
Building the documentation is driven by the :file:`docs/Makefile`.
With your :kbd:`midoss-docs` environment activated,
use:

.. _reStructuredText: https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html
.. _Sphinx: https://www.sphinx-doc.org/en/master/

.. code-block:: bash

    (midoss-docs)$ make clean html

to do a clean build of the documentation.
The output looks something like::

  Removing everything under '_build'...
  Running Sphinx v1.7.5
  making output directory...
  loading pickled environment... not yet created
  building [mo]: targets for 0 po files that are out of date
  building [html]: targets for 3 source files that are out of date
  updating environment: 3 added, 0 changed, 0 removed
  reading sources... [100%] index
  looking for now-outdated files... none found
  pickling environment... done
  checking consistency... done
  preparing documents... done
  writing output... [100%] index
  generating indices...
  writing additional pages... search
  copying static files... done
  copying extra files... done
  dumping search index in English (code: en) ... done
  dumping object inventory... done
  build succeeded.

  The HTML pages are in _build/html.

The HTML rendering of the docs ends up in :file:`docs/_build/html/`.
You can open the :file:`index.html` file in that directory tree in your browser to preview the results of the build.
To preview in Firefox from the command-line you can do:

.. code-block:: bash

    (midoss-docs)$ firefox _build/html/index.html

If you have write access to the `repository`_ on GitHub,
whenever you push changes to GitHub the documentation is automatically re-built and rendered at https://midoss-docs.readthedocs.io/en/latest/.


.. _MIDOSS-DocsLinkCheckingTheDocumentation:

Link Checking the Documentation
===============================

.. image:: https://github.com/MIDOSS/docs/workflows/sphinx-linkcheck/badge.svg
    :target: https://github.com/MIDOSS/docs/actions?query=workflow%3Asphinx-linkcheck
    :alt: Sphinx linkcheck

Use the commmand:

.. code-block:: bash

    (midoss-docs)$ make linkcheck

to check the documentation for broken links.
The output looks something like::

  Running Sphinx v3.2.1
  making output directory... done
  loading intersphinx inventory from https://salishsea-nowcast.readthedocs.io/en/latest/objects.inv...
  building [mo]: targets for 0 po files that are out of date
  building [linkcheck]: targets for 9 source files that are out of date
  updating environment: [new config] 9 added, 0 changed, 0 removed
  reading sources... [100%] python_code/library_code
  looking for now-outdated files... none found
  pickling environment... done
  checking consistency... done
  preparing documents... done
  writing output... [ 11%] CONTRIBUTORS
  (line   20) ok        https://scarp.ubc.ca/people/stephanie-chang
  (line   21) ok        https://www.dal.ca/faculty/agriculture/engineering/faculty-staff/our-faculty/haibo-niu.html
  (line   19) ok        https://www.eoas.ubc.ca/~sallen/
  writing output... [ 22%] TifFiles
  (line   35) ok        https://www.earthdatascience.org/courses/earth-analytics-python/lidar-raster-data/open-lidar-raster-python/
  writing output... [ 33%] contributing
  (line   13) ok        https://creativecommons.org/licenses/by/4.0/
  (line   13) ok        https://docs.python.org/3.8/
  (line   13) ok        https://github.com/MIDOSS/docs/issues
  (line   13) ok        https://midoss-docs.readthedocs.io/en/latest/
  (line   13) ok        https://github.com/MIDOSS/docs/actions?query=workflow%3Asphinx-linkcheck
  (line   38) ok        https://github.com/MIDOSS/docs
  (line   13) ok        https://github.com/MIDOSS/docs
  (line   44) ok        https://github.com/MIDOSS/docs
  (line   78) ok        https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh
  (line   58) ok        https://github.com/MIDOSS/docs
  (line   46) ok        https://readthedocs.org/projects/midoss-docs/builds/
  (line   93) ok        https://conda.io/en/latest/
  (line  251) ok        https://git-scm.com/
  (line   93) ok        https://docs.conda.io/en/latest/miniconda.html
  (line  139) ok        https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html
  (line  139) ok        https://www.sphinx-doc.org/en/master/
  (line  277) ok        https://github.com/MIDOSS/docs/blob/master/CONTRIBUTORS.rst
  (line   13) ok        https://readthedocs.org/projects/midoss-docs/badge/?version=latest
  (line   13) ok        https://img.shields.io/badge/python-3.6+-blue.svg
  (line   13) ok        https://img.shields.io/badge/version%20control-git-blue.svg?logo=github
  (line   13) ok        https://img.shields.io/badge/license-CC--BY-lightgrey.svg
  (line   13) ok        https://github.com/MIDOSS/docs/workflows/sphinx-linkcheck/badge.svg
  (line  193) ok        https://github.com/MIDOSS/docs/workflows/sphinx-linkcheck/badge.svg
  (line   58) ok        https://img.shields.io/badge/version%20control-git-blue.svg?logo=github
  (line   13) ok        https://img.shields.io/github/issues/MIDOSS/docs?logo=github
  (line  259) ok        https://img.shields.io/github/issues/MIDOSS/docs?logo=github
  writing output... [ 44%] index
  writing output... [ 55%] models/index
  writing output... [ 66%] models/mohid_on_graham
  (line   15) ok        https://docs.computecanada.ca/wiki/Graham
  (line   48) ok        https://docs.computecanada.ca/wiki/Storage_and_file_management
  (line   87) -ignored- https://github.com/MIDOSS/MIDOSS-MOHID-CODE
  (line   85) ok        https://github.com/MIDOSS/MOHID-Cmd
  (line   91) -ignored- https://github.com/MIDOSS/MIDOSS-MOHID-CODE
  (line   87) ok        https://github.com/MIDOSS/MOHID-Cmd
  (line   93) -ignored- https://github.com/MIDOSS/MIDOSS-MOHID-grid
  (line   85) ok        https://github.com/UBC-MOAD/moad_tools
  (line  121) ok        https://packaging.python.org/tutorials/installing-packages/#installing-to-the-user-site
  (line   89) ok        https://github.com/SalishSeaCast/NEMO-Cmd
  (line   15) ok        http://www.mohid.com/
  (line   95) ok        https://github.com/MIDOSS/MIDOSS-MOHID-config
  (line   91) ok        http://www.mohid.com/
  (line  197) ok        https://docs.computecanada.ca/wiki/Running_jobs#Interactive_jobs
  (line  188) ok        http://wiki.mohid.com/index.php?title=Mohid_Framework
  (line  188) ok        http://wiki.mohid.com/index.php?title=Mohid_Base_2
  (line  188) ok        http://wiki.mohid.com/index.php?title=Mohid_Water
  (line  188) ok        http://wiki.mohid.com/index.php?title=Mohid_Base_1
  writing output... [ 77%] models/wwatch3_on_graham
  (line  254) ok        https://salishsea-nowcast.readthedocs.io/en/latest/pkg_development.html#salishseanowcastdevelopmentenvironment
  (line  245) ok        https://salishsea-nowcast.readthedocs.io/en/latest/workers.html#makeww3currentfile-worker
  (line  258) ok        https://salishsea-nowcast.readthedocs.io/en/latest/workers.html#makeww3windfile-worker
  (line   83) ok        https://github.com/SalishSeaCast/SalishSeaWaves
  (line  245) ok        https://salishsea-nowcast.readthedocs.io/en/latest/workers.html#makeww3windfile-worker
  (line   15) ok        https://polar.ncep.noaa.gov/waves/wavewatch/
  writing output... [ 88%] python_code/index
  writing output... [100%] python_code/library_code
  (line   92) ok        https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html#imports
  (line   87) ok        https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html
  (line   20) ok        https://www.python.org/dev/peps/pep-0008/
  (line   94) ok        https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html#return-simplenamespace-from-functions
  (line   93) ok        https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html#public-and-private-objects

  build succeeded.

  Look for any errors in the above output or in _build/linkcheck/output.txt

:command:`make linkcheck` is run monthly via a `scheduled GitHub Actions workflow`_

.. _scheduled GitHub Actions workflow: https://github.com/MIDOSS/docs/actions?query=workflow%3Asphinx-linkcheck


.. _MIDOSS-DocsVersionControlRepository:

Version Control Repository
==========================

.. image:: https://img.shields.io/badge/version%20control-git-blue.svg?logo=github
    :target: https://github.com/MIDOSS/docs
    :alt: Git on GitHub

The MIDOSS documentation source files are available as a `Git`_ repository at https://github.com/MIDOSS/docs.

.. _Git: https://git-scm.com/


.. _MIDOSS-DocsIssueTracker:

Issue Tracker
=============

.. image:: https://img.shields.io/github/issues/MIDOSS/docs?logo=github
    :target: https://github.com/MIDOSS/docs/issues
    :alt: Issue Tracker

Documentation tasks,
bug reports,
and enhancement ideas are recorded and managed in the issue tracker at https://github.com/MIDOSS/docs/issues.


License
=======

.. image:: https://img.shields.io/badge/license-CC--BY-lightgrey.svg
    :target: https://creativecommons.org/licenses/by/4.0/
    :alt: Licensed under the Creative Commons Attribution 4.0 International License

The MEOPAR MIDOSS Project Documentation is Copyright 2018-2021 by the `MIDOSS project contributors`_,
The University of British Columbia,
and Dalhousie University.

.. _MIDOSS project contributors: https://github.com/MIDOSS/docs/blob/master/CONTRIBUTORS.rst

It is licensed under a `Creative Commons Attribution 4.0 International License`_.

.. _Creative Commons Attribution 4.0 International License: https://creativecommons.org/licenses/by/4.0/
