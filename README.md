This repository contains instructions for setting up a SPARQL endpoint to Freebase via Virtuoso as well as some useful materials about Freebase.

## Requirements
* OpenLink Virtuoso 7.2.5 (download from this [link](https://sourceforge.net/projects/virtuoso/files/virtuoso/7.2.5/virtuoso-opensource.x86_64-generic_glibc25-linux-gnu.tar.gz/download))
* Python 3 (required if using the provided Python script)

## Setup

### Freebase data dump

The latest offical data dump of Freebase can be downloaded [here](https://developers.google.com/freebase). However, in the official dump, the format of some literal types is not fully compatible with the N-Triples RDF standard (it's missing type decoration such as `^^<http://www.w3.org/2001/XMLSchema#integer>`), which may cause it to fail to load into triplestores like Virtuoso. We fixed this issue. Our processed Virtuoso DB file can be downloaded from [here](https://www.dropbox.com/s/q38g0fwx1a3lz8q/virtuoso_db.zip) or via wget (WARNING: 53G+ disk space is needed):

```
wget https://www.dropbox.com/s/q38g0fwx1a3lz8q/virtuoso_db.zip
```

In case you'd like to load your own RDF into Virtuoso, see [here](http://vos.openlinksw.com/owiki/wiki/VOS/VirtBulkRDFLoader) for instructions. If you prefer some other triplestore, contact [us](mailto:su.809@osu.edu) for the fixed RDF file for Freebase if needed. 

### Managing the Virtuoso service

We provide a wrapper script (`virtuoso.py`, adapted from [Sempre](https://github.com/percyliang/sempre)) for managing the Virtuoso service. To use it, first change the `virtuosoPath` in the script to your local Virtuoso directory. Assuming the Virtuoso db file is located in a directory named `virtuoso_db` under the same directory as the script `virtuoso.py` and 3001 is the intended HTTP port for the service, to start the Virtuoso service:

```
python3 virtuoso.py start 3001 -d virtuoso_db
```

and to stop a currently running service at the same port:

```
python3 virtuoso.py stop 3001
```

A server with at least 100 GB RAM is recommended. You may adjust the maximum amount of RAM the service may use and other configurations via the provided script.

## Useful materials about Freebase
- Some basic concepts: https://developers.google.com/freebase/guide/basic_concepts
- A brief review of Freebase: https://github.com/nchah/freebase-mql
- [RDF](https://www.w3.org/TR/rdf11-concepts/) and [SPARQL](https://www.w3.org/TR/sparql11-query/)
- Freebase is strongly typed (in terms of terminology, type=class, property=relation=predicate). Each property has one and only one _domain type_ (the type of the subject entity) and _range type_ (the type of the object entity). For example, the property `people.person.place_of_birth`'s domain type is `people.person` and range type is `location.location`. 
- A list of frequently used relations for meta-information:
  - `common.topic`: the type of all entities. An item is an entity if it has this type.
  - `type.object.name`: the canonical name of an item (entity/type/property). Filter by the language decorator (e.g., `@en`) if only a certain language is of interest.
  - `common.topic.alias`: a list of common aliases of an entity.
  - `common.topic.description`: a textual description of an entity.
  - `type.object.type`: the list of types of an entity.
  - `type.type.properties`: the list of properties with a type as the domain type.
  - `type.type.expected_by`: the list of properties with a type as the range type.
  - `type.property.expected_type`: the range type of a property.
  - `freebase.type_profile.strict_included_types` and `freebase.type_profile.included_types`: the parent types of a type.
  - `freebase.type_hints.mediator`: true if a type is a CVT (Compound Value Type), Freebase's way of model Neo-Davidsonian event semantics via event reification.
  - `type.property.master_property` and `type.property.reverse_property`: Freebase has twin properties. These two properties let you find the twin.
