Ohm Scripts
===========

Lua scripts for creating, updating and deleting Ohm compatible
objects in Redis.

Description
-----------

Ohm is a library for storing objects in Redis, a persistent
key-value database. These Lua scripts can be sent to Redis with the
EVAL command, and they abstract the behavior of keeping indices
and unique indices in sync with the object.

save.lua
--------

This script receives four parameters, all encoded with
MessagePack. The decoded values are used for saving a model
instance in Redis, creating or updating a hash as needed and
updating zero or more sets (indices) and zero or more hashes
(unique indices).

### model

Table with three attributes:

   - id (model instance id)
   - key (hash where the attributes will be saved)
   - name (model name)

### attrs

Array with attribute/value pairs.

### indices

Fields and values to be indexed. Each key in the indices table is
mapped to an array of values. One index is created for each
field/value pair.

### uniques

Fields and values to be indexed as unique. Unlike indices, values
are not enumerable. If a field/value pair is not unique (i.e., if
there was already a hash entry for that field and value), an error
is returned with the UniqueIndexViolation message and the field
that triggered the error.

delete.lua
----------

This script receives three parameters, all encoded with
MessagePack. The decoded values are used for deleting a model
instance in Redis and removing any reference to it in sets
(indices) and hashes (unique indices).

### model

Table with three attributes:

  - id   (model instance id)
  - key  (hash where the attributes will be saved)
  - name (model name)

### uniques

Fields and values to be removed from the unique indices.

### tracked

Keys that share the lifecycle of this model instance, that should
be removed as this object is deleted.

Usage
-----

These scripts are intended to be used in the context of other
libraries. Right now they are implemented by
[Ohm](http://ohm.keyvalue.org). Other libraries are encouraged to
use these scripts, with the benefit of achieving cross language
compatibility.
