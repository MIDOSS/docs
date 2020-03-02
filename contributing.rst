.. Copyright 2018-2020 The MIDOSS project contributors,
.. the University of British Columbia, and Dalhousie University.
..
.. Licensed under a Creative Commons Attribution 4.0 International License
..
..    https://creativecommons.org/licenses/by/4.0/


.. _MIDOSS-DocsContributing:

**************************
Contributing to These Docs
**************************

Additions,
improvements,
and corrections to these docs are *always* welcome.

The quickest way to fix typos, etc. on existing pages is to use the :guilabel:`Edit on Bitbucket` link in the upper right corner of the page to get to the online editor for the page on `Bitbucket`_.

For more substantial work,
and to add new pages,
the instructions below explain how to:

* clone the repository from `Bitbucket`_

* set up a conda environment in which you can build the docs locally instead of having to push commits to Bitbucket to trigger a `build on readthedocs.org`_

* build the docs with your changes,
  and preview them in Firefox

.. _Bitbucket: https://bitbucket.org/midoss/docs
.. _build on readthedocs.org: https://readthedocs.org/projects/midoss-docs/builds/


.. _MIDOSS-DocsGettingTheRepo:

Getting the Repo
================

Clone the MIDOSS documentation `repository`_ from Bitbucket with:

.. _repository: https://bitbucket.org/midoss/docs

.. code-block:: bash

    $ hg clone ssh://hg@bitbucket.org/midoss/docs

or

.. code-block:: bash

    $ hg clone https://you_userid@bitbucket.org/midoss/docs

if you don't have `ssh key authentication`_ set up on Bitbucket
(replace :kbd:`you_userid` with you Bitbucket userid,
or copy the link from the :guilabel:`Clone` action pop-up on the `repository`_ page).

.. _ssh key authentication: https://confluence.atlassian.com/bitbucket/set-up-an-ssh-key-728138079.html


.. _MIDOSS-DocsBuildEnvironment:

Docs Build Environment
======================

Setting up an isolated docs build environment using `Conda`_ is recommended.
Assuming that you have the `Anaconda Python Distribution`_ or `Miniconda3`_ installed,
you can create and activate an environment called :kbd:`midoss-docs` that will have all of the Python packages necessary for building the documentation with the commands:

.. _Conda: https://conda.io/docs/
.. _Anaconda Python Distribution: https://www.anaconda.com/download/
.. _Miniconda3: https://conda.io/docs/install/quick.html

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

The MIDOSS documentation is written in `reStructuredText`_ and converted to HTML using `Sphinx`_.
Creating a :ref:`MIDOSS-DocsBuildEnvironment` as described above includes the installation of Sphinx.
Building the documentation is driven by the :file:`docs/Makefile`.
With your :kbd:`midoss-docs` environment activated,
use:

.. _reStructuredText: http://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html
.. _Sphinx: http://www.sphinx-doc.org/en/master/

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

If you have write access to the `repository`_ on Bitbucket,
whenever you push changes to Bitbucket the documentation is automatically re-built and rendered at https://midoss-docs.readthedocs.io/en/latest/.


.. _MIDOSS-DocsLinkCheckingTheDocumentation:

Link Checking the Documentation
===============================

Use the commmand:

.. code-block:: bash

    (midoss-docs)$ make linkcheck

to check the documentation for broken links.
The output looks something like::

  Running Sphinx v1.7.5
  loading pickled environment... done
  building [mo]: targets for 0 po files that are out of date
  building [linkcheck]: targets for 3 source files that are out of date
  updating environment: 0 added, 1 changed, 0 removed
  reading sources... [100%] contributing
  looking for now-outdated files... none found
  pickling environment... done
  checking consistency... done
  preparing documents... done
  writing output... [ 33%] CONTRIBUTORS
  (line   11) ok        https://www.eoas.ubc.ca/~sallen/
  (line   13) ok        https://www.dal.ca/faculty/agriculture/engineering/faculty-staff/our-faculty/haibo-niu.html
  (line   12) ok        https://scarp.ubc.ca/people/stephanie-chang
  writing output... [ 66%] contributing
  (line   55) ok        https://confluence.atlassian.com/bitbucket/set-up-an-ssh-key-728138079.html
  (line   27) ok        https://readthedocs.org/projects/midoss-docs/builds/
  (line   19) ok        https://bitbucket.org/midoss/docs
  (line   25) ok        https://bitbucket.org/midoss/docs
  (line   41) ok        https://bitbucket.org/midoss/docs
  (line   67) ok        https://conda.io/docs/
  (line   55) ok        https://bitbucket.org/midoss/docs
  (line   67) ok        https://www.anaconda.com/download/
  (line   67) ok        https://conda.io/docs/install/quick.html
  (line  109) ok        http://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html
  (line  109) ok        http://www.sphinx-doc.org/en/master/
  (line  156) ok        https://midoss-docs.readthedocs.io/en/latest/
  writing output... [100%] index

  build succeeded.

  Look for any errors in the above output or in _build/linkcheck/output.txt
