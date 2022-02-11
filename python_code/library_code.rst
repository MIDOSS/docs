.. Copyright 2018 – present by The MIDOSS project contributors,
.. the University of British Columbia, and Dalhousie University.
..
.. Licensed under a Creative Commons Attribution 4.0 International License
..
..    https://creativecommons.org/licenses/by/4.0/


.. _GuidelinesAndBestPracticesForWritingLibraryCode:

******************************************************
Guidelines and Best Practices for Writing Library Code
******************************************************

The notes in this section are about writing readable,
maintainable Python code that your future self and other people will be able to use,
maintain,
and improve.

A primary guide for writing Python code is `PEP 8 -- Style Guide for Python Code`_.

.. _PEP 8 -- Style Guide for Python Code: https://www.python.org/dev/peps/pep-0008/


Python 3
========

The MIDOSS project uses Python 3.
Your should write and test your code using Python 3.


.. _LibraryCodeStandardCopyrightHeaderBlock:

Standard Copyright Header Block
===============================

Every Python library module show start with our standard copyright notice::

  # Copyright 2018 – present by the MIDOSS project contributors,
  # and the University of British Columbia, and Dalhousie University.
  #
  # Licensed under the Apache License, Version 2.0 (the "License");
  # you may not use this file except in compliance with the License.
  # You may obtain a copy of the License at
  #
  #    http://www.apache.org/licenses/LICENSE-2.0
  #
  # Unless required by applicable law or agreed to in writing, software
  # distributed under the License is distributed on an "AS IS" BASIS,
  # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  # See the License for the specific language governing permissions and
  # limitations under the License.

The copyright notice is marked as comments with the :kbd:`#` character at the beginning of lines rather than enclosing the block in triple quotes so that it will not be interpreted by code introspection tools as the module docstring.

The exception to using the above notice is if the module contains code that we have copied from somewhere.
In that case the copyright ownership needs to be changed to make appropriate attribution.
The license notice may also need to be changed if the code is released under a license other than Apache 2.0.
If you have questions about the attribution and licensing of a piece of code,
please talk to Doug.

Sphinx documentation files in code and notebook repos should also start with the same standard copyright notice::

  .. Copyright 2018 – present by the MIDOSS project contributors,
  .. and the University of British Columbia, and Dalhousie University.
  ..
  .. Licensed under the Apache License, Version 2.0 (the "License");
  .. you may not use this file except in compliance with the License.
  .. You may obtain a copy of the License at
  ..
  ..    http://www.apache.org/licenses/LICENSE-2.0
  ..
  .. Unless required by applicable law or agreed to in writing, software
  .. distributed under the License is distributed on an "AS IS" BASIS,
  .. WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  .. See the License for the specific language governing permissions and
  .. limitations under the License.

though the comment indicator at the beginning of the lines is,
of course,
different.


Other Guidelines
================

Please see the `Salish Sea project tools docs code guidelines section`_ for more guidelines,
in particular:

.. _Salish Sea project tools docs code guidelines section: https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html

* `Imports`_
* `Public and Private Objects`_
* `Returning Namespaces from Functions`_

.. _Imports: https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html#imports
.. _Public and Private Objects: https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html#public-and-private-objects
.. _Returning Namespaces from Functions: https://salishsea-meopar-tools.readthedocs.io/en/latest/python_packaging/library_code.html#return-simplenamespace-from-functions
