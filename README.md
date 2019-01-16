# Getting started with JSound 2.0

This tutorial gives an introduction to JSound 2.0, starting with its compact syntax.

# JSound's type system

JSound 2.0 is a schema language that validates and annotates JSON documents.

Whereas some other JSON Schema languages are based on filtering JSON documents, a JSound schema is best seen as a collection of type declarations.

JSound supports four kinds of types corresponding to JSON's spirit:

- atomic types, with a selection of builtin, ready-to-use types: string, integer, date, hexBinary to name a few
- object types, to validate and annotate JSON objects
- array types, to validate and annotate JSON arrays
- union types, to combine several types and union their value spaces

# A hello world

Let us start with a simple example.

This is a JSON instance:

```
{
  "name" : "JSound"
}
```

Imagine we would like to validate such instances against a type, let us call it my-type, and say that for objects in that type's value space, the name field must be a string.

The type declaration itself, in JSound's comptact syntax, mimics the instance, like so:

```
{
  "name" : "string"
}
```

Now, if we want to put it inside a JSound schema and give the type a name, we associate this type declaration with the type name like so:

```
{
  "my-type" : {
    "name" : "string"
  }
}
```
