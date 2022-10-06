============================
JSON Schema LD specification
============================

.. toctree::
   :hidden:
   :maxdepth: 1
   :caption: Table of contents:

   self
   jsc_ld_installation
   jsc_ld_configuration
   jsc_ld_syntax
   supported_jsc_kws


JSON Schema for Linked Data, hereafter JSC-LD/jsc-ld, is a syntactic sugar for `JSON Schema <https://json-schema.org/>`__ to enable generative interoperability
by means of representing JSON schema in RDF vocabularies (`RDF Schema <https://www.w3.org/TR/rdf-schema/>`__) and RDF
shapes (`SHACL <https://www.w3.org/TR/shacl/>`_).

A JSON Schema is a declarative vocabulary specifies a number of rules to describe what objects serialized in JSON based
format should look like. With JSC-LD, domain models and application profiles are extracted from existing
implementation model in JSON Schema and represented in `RDF Schema <https://www.w3.org/TR/rdf-schema/>`__ and `Shacl shapes <https://www.w3.org/TR/shacl/>`__.

**Built With**

.. only:: html

    .. image:: _static/nodejs.jpg
        :height: 50px
        :width: 100px
        :target: https://www.typescriptlang.org/
    .. image:: _static/typescript.png
        :height: 50px
        :width: 100px
        :target: https://nodejs.org/en/
    .. image:: _static/n3js.png
        :height: 50px
        :width: 100px
        :target: http://rdf.js.org/N3.js/

Getting Started
===============

Prerequisites
-------------

* :ref:`jsc_ld_installation`
* :ref:`jsc_ld_configuration`
* :ref:`jsc_ld_syntax`

Usage
-----

.. code-block:: none

      $ jsc-ld --source json_schema.js --out out --config config.js
      $ jsc-ld --source json_schema.js --config config.js
      $ jsc-ld -s json_schema.js -c config.js

    Options

      -c, --config config_file                     JSC-LD configuration file
      -s, --source path/to/source/file|directory   Path to a JSON schema file or a directory contains JSON schema files
      -o, --out path/to/directory                  Path to output directory defaults to "out"
      -h, --help                                   Display this usage guide

Limitations
-----------

JSC-LD is developed upon the latest draft ``2020-12`` to describe data formats. Some custom keywords defined in `a prior draft <https://json-schema.org/specification-links.html>`_ may not be supported.
Keywords that are supported by JSC-LD can be found :ref:`supported_jsc_kws`.

contribute
----------
Do not hesitate to `report a bug <https://github.com/jiaoxlong/jsc-ld/issues>`_.

Lisense
-------

This code is copyrighted by IDLab, Ghent University – imec and released under the MIT license.

Contact
-------
`Jiao Long <mailto:Jiao.Long@UGent.be>`_, `Brecht Van de Vyvere <mailto:brecht.vandevyvere@ugent.be>`_ and `Pieter Colpaert <mailto:pieter.colpaert@ugent.be>`_

IDLab, Ghent University – imec



