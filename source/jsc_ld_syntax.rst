.. _jsc_ld_syntax:

================================
JSON Schema LD Annotation Syntax
================================

    PLACE_HOLDER

JSON-LD annotation keywords
===========================

    PLACE_HOLDER

ld.id
-----

    A property can be renamed by annotating it with ``ld.id`` JSC-LD syntax for the sake of semantic.

.. schema_example::

    {
        "id": {
            "ld.id": "https://example.com/identifier",
            "type": "string"
        }
    }

.. note::

    In this example, the hypothetical ``id`` property is mapped to ``identifier`` in a URI, of which name prefix is predefined in a ref:`jsc_ld_configuration` file with ``base_url``.
    Instead of giving a full path, one may also use ``"ld.id": "identifier"`` or ``"ld.id": ${base_prefix}:identifier"`` as a shortcut.

    ``ld.id`` can be used in combination with ``ld.existing`` when a property already exists in a vocabulary or ``ld.class`` when a property needs to be casted into a Class in RDF.

ld.title ld.description and ld.comment
--------------------------------------

    ``ld.title``, ``ld.description``, ``ld.comment`` act as counterpart of "title", "description", and "comment" in JSON Schema, respectively.
    See `JSON Schema Annotations <https://json-schema.org/understanding-json-schema/reference/generic.html#id2>`_ for more details
    These JSON-LD keywords are used to overwrite original annotations in JSON Schema or add annotations if needed.


.. schema_example::

    {
        "id": {
            "type": "string",
            "description":"bike id",
            "ld.title": "bike_id",
            "ld.description": "A unique identifier representing a bike stored, managed and accessed in the database.",
            "ld.comment": "The id must be unique among all identifiers used in the system."
        }
    }

ld.existing
-----------

    Following `the best practise of ontology development <https://protege.stanford.edu/publications/ontology_development/ontology101.pdf>`_,
    before creating an ontology term, one should consider to reuse existing vocabularies for your domain to avoid "re-inventing the wheel".
    In the ``ld.id`` example, the property "id" is renamed to "identifier". However, 'id' is defined in many other domain vocabularies.
    In fact, One can simply reuse an equivalent term e.g. `"identifier" from DublinCore <http://purl.org/dc/terms/identifier>`_ instead of creating one.

.. schema_example::

    {
        "id": {
            "ld.id": "http://purl.org/dc/terms/identifier",
            "ld.existing": "true",
            "type": "string",
            "description": "A unique identifier representing a bike stored, managed and accessed in the database."
        }
    }


.. note::
    When a property tagged with ``ld.existing`` in a JSON Schema, all the annotations associated with the property in the JSON Schema will not be present in RDF to avoid modification on existing vocabularies.

id.domain id.range
------------------
    JSC-LD parses a JSON Schema along its hierarchical structure.
    It is possible to annotate a property with ``ld.domain`` and/or ``ld.range`` to state any resource that has this property
    or any value of this property is an instance of a class, respectively. When the classes is not declared in a prior step, they will be created during conversion.

.. schema_example::

    {
        "bikes": {
            "type": "object",
            "properties": {
                "bike_id": {
                    "type": "number"
                },
                "bike_location":{
                    "type": "string",
                    "description": "The geo location of the bike.",
                    "ld.domain": "https://example.com/Bike",
                    "ld.range": "https://example.com/Location"
                }
            }
        }
    }

RDF vocabulary

.. code-block::

    @prefix ex: <https://www.example.com/>.
    @prefix dcterms: <http://purl.org/dc/terms/>.
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.

    ex:bikes rdf:type rdf:Property;
        ex:bikes rdfs:label "bikes";
    ex:bike_id rdf:type rdf:Property;
        rdfs:label "bike_id";
        rdfs:range xsd:integer.
    ex:bike_location rdf:type rdf:Property;
        dcterms:description "The geo location of the bike.";
        rdfs:label "bike_location";
        rdfs:domain ex:Bike;
        rdfs:range ex:Location.
    ex:Bike rdf:type rdfs:Class;
        rdfs:label "Bike".
    ex:Location rdf:type rdfs:Class;
        rdfs:label "Location".

.. note::
    This is a dummy example about the usage of ``ld.domain`` and ``ld.range``.
    Defining classes can be better handled by using the combination of ``ld.association`` and ``ld.range``.

ld.ignore
=========

     ``ld.ignore`` is used to exclude certain properties and their sub-properties from a JSON Schema and **not** express them in RDF.

.. schema_example::

    {
        "id": {
            "ld.ignore": "true",
            "type": "string",
            "description": "A unique identifier representing a bike stored, managed and accessed in the database."
        }
    }

.. caution::

    When a property tagged with ``ld.ignore``, not only the tagged property but also its sub-properties are excluded in the vocabulary.

ld.included
===========

    ``ld.included`` is used to exclude certain properties only at one level but continue parsing their sub-properties.

.. schema_example::

    {
        "bikes": {
            "ld.included": "true",
            "type": "object",
            "properties": {
                "bike_id": {
                    "type": "number",
                    "description": "an unique identifier of the bike."
                },
                "bike_location":{
                    "type": "string",
                    "description": "The geo location of the bike."
                }
            }
        }
    }

RDF vocabulary

.. code-block::

    @prefix ex: <https://www.example.com/>.
    @prefix dcterms: <http://purl.org/dc/terms/>.
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.

    ex:bike_id rdf:type rdf:Property;
        dcterms:description "an unique identifier of the bike.";
        rdfs:label "bike_id";
        rdfs:range xsd:integer.
    ex:bike_location rdf:type rdf:Property;
        dcterms:description "The geo location of the bike.";
        rdfs:label "bike_location";
        rdfs:range xsd:string.

ld.enum
=======

    ```ld.enum`` is defined in JSC-LD build-in syntax but still under use case evaluation.

    The enum keyword can be applied to  is used to restrict a value of a property to a fixed set of values.
    JSC-LD processes properties with enum keyword with skos:Concept and skos:ConceptSchema.

ld.association
==============

    ``ld.association`` is used in combination with ``ld.range`` and especially designed for complex and nested JSON Schemas.
    Consider the bike example given before, without ``ld.included`` annotation, "bikes" and its sub-properties "bike_id" and "bike_location"
    will be considered as instances of rdf:Property without indicating connections in between by default.
    By adding ``ld.association`` and ``ld.range``, JSC-LD will create an association class "ex:Bike"  to connect them all.

.. schema_example::

    {
        "bikes": {
            "ld.association": "true",
            "ld.range": "https://www.example.com/Bike",
            "type": "object",
            "properties": {
                "bike_id": {
                    "type": "number"
                },
                "bike_location":{
                    "type": "string"
                }
            }
        }
    }

RDF vocabulary

.. code-block::

    @prefix ex: <https://www.example.com/>.
    @prefix dcterms: <http://purl.org/dc/terms/>.
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.

    ex:bikes rdf:type rdf:Property;
        rdfs:range ex:Bike.
        rdfs:label "bikes";

    ex:Bike rdf:type rdf:Class;
        rdfs:label "Bike".

    ex:bike_id rdf:type rdf:Property;
        rdfs:domain ex:Bike;
        rdfs:range xsd:integer.
        rdfs:label "bike_id";

    ex:bike_location rdf:type rdf:Property;
        rdfs:domain ex:Bike;
        rdfs:range xsd:string.
        rdfs:label "bike_location";


ld.class
==============

    In addition to ``ld.association``, ``ld.class`` is also used to create a class, but it behaves differently.
    Fundamentally, it combines the features of ``ld.association`` and ``ld.included``.

.. schema_example::

    {
        "bikes": {
            "ld.id": "https://www.example.com/Bike",
            "ld.class": "true",
            "type": "object",
            "properties": {
                "bike_id": {
                    "type": "number"
                },
                "bike_location":{
                    "type": "string"
                }
            }
        }
    }

.. code-block::

    @prefix ex: <https://www.example.com/>.
    @prefix dcterms: <http://purl.org/dc/terms/>.
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.

    ex:Bike rdf:type rdf:Class;
        rdfs:label "Bike".

    ex:bike_id rdf:type rdf:Property;
        rdfs:domain ex:Bike;
        rdfs:range xsd:integer.
        rdfs:label "bike_id";

    ex:bike_location rdf:type rdf:Property;
        rdfs:domain ex:Bike;
        rdfs:range xsd:string.
        rdfs:label "bike_location";


ld.geoJsonFeature
=================

    Handling geometry type properties can be quite tricky. Below is a snapshot of station_area from the JSON Schema for General Bikeshare Feed Specification(GBFS) feeds.
    For the sake of simplicity, adding ``ld.geoJsonFeature`` will make "station_area" property have a value that is a instance of geosparql:Geometry by default.
    When the geosparql:Geometry is not suitable, one may change it to another Geometry class with ``ld.range``.

.. schema_example::

    {
        "station_area": {
            "ld.geoJsonFeature": true,
            "ld.range" : "http://www.opengis.net/ont/geosparql#Geometry",
            "description": "A multipolygon that describes the area of a virtual station (added in v2.1-RC).",
            "type": "object",
            "required": ["type", "coordinates"],
            "properties": {
                "type": {
                    "type": "string",
                    "enum": ["MultiPolygon"]
                },
                "coordinates": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "array",
                            "minItems": 4,
                            "items": {
                                "type": "array",
                                "minItems": 2,
                                "items": {
                                    "type": "number"
                                }
                            }
                        }
                    }
                }
            }
        }
    }


.. code-block::

    @prefix gbfs: <https://w3id.org/gbfs#>.
    @prefix dcterms: <http://purl.org/dc/terms/>.
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>.
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>.
    @prefix geosparql: <http://www.opengis.net/ont/geosparql#>.

    gbfs:stationArea dcterms:description "A multipolygon that describes the area of a virtual station (added in v2.1-RC).";
        rdf:type rdf:Property;
        rdfs:label "stationArea";
        rdfs:range geosparql:Geometry.


