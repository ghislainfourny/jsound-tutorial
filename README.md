# Getting started with JSound 2.0

This tutorial gives an introduction to JSound 2.0, starting with its compact syntax.

## JSound's type system

JSound 2.0 is a schema language that validates and annotates JSON documents.

Whereas some other JSON Schema languages are based on filtering JSON documents, a JSound schema is best seen as a collection of type declarations.

JSound supports four kinds of types corresponding to JSON's spirit:

- atomic types, with a selection of builtin, ready-to-use types: string, integer, date, hexBinary to name a few
- object types, to validate and annotate JSON objects
- array types, to validate and annotate JSON arrays
- union types, to combine several types and union their value spaces

## A hello world

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

Note that this type declaration does not prevent further fields from being present, which is the default behavior. Also, it does not require the field name to be present -- but if it is, it must be a string.

These are a few instances valid against my-type:

```
{ "name" : "James Kirk" }
{ "name" : "Beverly Crusher" }
{ "name" : "Spock", "origin" : "Vulcan", "age" : 234 }
{ "spaceship" : "USS Discovery", "name" : "Michael Burnham" }
{ }
{ "century" : 23 }
```

And a few that are not:
```
{ "name" : 1 }
{ "name" : null }
{ "name" : [ "Spock" ], "age" : 123 }
{ "bar" : 1, "name" : { "first" : "James", "last" : "Kirk" } }
```

## More on object type declarations

### Requiring a field

In order to make the name field required, we can add an exclamation mark, like so:

```
{
  "my-type" : {
    "name!" : "string"
  }
}
```

Then, these documents become invalid:

```
{ }
{ "century" : 23 }
```

Type names containing ! are possible, but this requires the advanced syntax, covered later.

### Default value

We can also supply a default value, like so:

```
{
  "my-type" : {
    "name" : "string=N/A"
  }
}
```

Then the following instances would be valid:

```
{ }
{ "century" : 23 }
```

But an annotation step would actual produce a different output, populating default values:

```
{ "name" : "N/A" }
{ "century" : 23, "name" : "N/A"  }
```

Default values containing = are possible, but this requires the advanced syntax, covered later.

More advanced features are available, such as not allowing extra fields, but we will cover this later with the advanced syntax.

### nullability

A question mark indicates that null is allowed:

```
{
  "my-type" : {
    "name" : "string?"
  }
}
```

This instance is then valid:

```
{ "name" : null }
```

Technically, what is defined here is a union type with the member types string and null.

## More builtin atomic types

We used so far the string builtin type. There are a few others builtin atomic types.

The validation of an atomic type is done based on its lexical value. Whether it is quoted or not is totally irrelevant, as long as the input is well-formed JSON.

We provide below the list of builtin types, and for simplicity simple give examples of valid instances.

For all examples, we give a very simple schema with just one field that has the builtin type being explained.

For further reference, these types (except null) are documented in the [XML Schema](https://www.w3.org/TR/xmlschema11-2) specification.

### integer

```
{
  "my-type" : {
    "field!" : "integer"
  }
}
```

The following instances are valid:

```
{ "field" : 1 }
{ "field" : 3 }
{ "field" : "12" }
{ "field" : 0 }
{ "field" : -3 }
{ "field" : 123450987234502983452345 }
```
### decimal

```
{
  "my-type" : {
    "field!" : "decimal"
  }
}
```
The following instances are valid:

```
{ "field" : 1 }
{ "field" : 3 }
{ "field" : 12.3 }
{ "field" : 0 }
{ "field" : "-334.23" }
{ "field" : 123450987234502983452345.23405978234059872345023945809823745 }
```

### double

```
{
  "my-type" : {
    "field!" : "double"
  }
}
```
The following instances are valid:
```
{ "field" : 1 }
{ "field" : 3 }
{ "field" : 12.3 }
{ "field" : "2345e78" }
{ "field" : -1234.2345e-345 }
```
### boolean
```
{
  "my-type" : {
    "field!" : "boolean"
  }
}
```
The following instances are valid:

```
{ "field" : true }
{ "field" : "false" }
```

### anyURI

```
{
  "my-type" : {
    "field!" : "anyURI"
  }
}
```
The following instances are valid:

```
{ "field" : "http://www.example.com" }
```

(Actually, any string is accepted, but it signals an intent to include URIs).

### base64Binary

```
{
  "my-type" : {
    "field!" : "base64Binary"
  }
}
```
The following instances are valid:

```
{ "field" : "SGVsbG8sIHdvcmxk" }
```

### hexBinary

```
{
  "my-type" : {
    "field!" : "hexBinary"
  }
}
```
The following instances are valid:

```
{ "field" : "8a08b0c0908f" }
```

### date

```
{
  "my-type" : {
    "field!" : "date"
  }
}
```
The following instances are valid:

```
{ "field" : "2019-01-19" }
```


### dateTime

```
{
  "my-type" : {
    "field!" : "dateTime"
  }
}
```
The following instances are valid:

```
{ "field" : "2019-01-19T12:00:00" }
{ "field" : "2019-01-19T12:34:56.789" }
{ "field" : "2019-01-19T12:00:00.000Z" }
{ "field" : "2019-01-19T12:00:00.000+02:00" }
```

### time

```
{
  "my-type" : {
    "field!" : "time"
  }
}
```
The following instances are valid:

```
{ "field" : "12:00:00" }
{ "field" : "12:34:56.789" }
{ "field" : "12:00:00.000Z" }
{ "field" : "12:00:00.000+02:00" }
```
### dateTimeStamp


```
{
  "my-type" : {
    "field!" : "dateTimeStamp"
  }
}
```
The following instances are valid:

```
{ "field" : "2019-01-19T12:34:56.789-08:00" }
{ "field" : "2019-01-19T12:00:00.000Z" }
{ "field" : "2019-01-19T12:00:00.000+02:00" }
```

### duration

```
{
  "my-type" : {
    "field!" : "duration"
  }
}
```
The following instances are valid:

```
{ "field" : "P1Y2M3DT4H5M6S" }
{ "field" : "P1Y2M3D" }
{ "field" : "P3D" }
{ "field" : "P3DT4H5M6S" }
{ "field" : "PT4H5M6S" }
{ "field" : "PT6S" }
```


### null

```
{
  "my-type" : {
    "field!" : "null"
  }
}
```
The following instances are valid:

```
{ "field" : null }
{ "field" : "null" }
```

## Arrays

Array types can be defined in the simplified syntax as well by specifying the type of its members.

For example, we can define an array of integers like so:

```
{
  "my-type" : {
    "field!" : [ "integer" ]
  }
}
```

The following instances are valid:

```
{ "field" : [ 1, 2, 3, 4 ] }
{ "field" : [ 1 ] }
{ "field" : [ ] }
```

### Arrays of objects

We can also nest type declarations and, for example, define an array of objects matching a certain type:

```
{
  "my-type" : {
    "field!" : [
      {
        "first!" : "string",
        "last!" : "string=N/A",
        "age!" : "integer"
      }
    ]
  }
}
```

The following instances are valid -- as you can see, the schema looks very similar to them, which makes it easy to read:

```
{ "field" : [ { "first" : "James", "last" : "Kirk", "age" : 30 } ]
{
  "field" : [
    { "first" : "James", "last" : "Kirk", "age" : 30 },
    { "first" : "Kathryn", "last" : "Janeway", "age" : 50 },
    { "first" : "Spock", "age" : 234 }
  ]
}
{ "field" : [ ] }
```

It is also possible to name the object type and refer to it. The following schema is equivalent:

```
{
  "person" : {
    "first!" : "string",
    "last!" : "string=N/A",
    "age!" : "integer"
  },
  "list" : {
    "field!" : [ "person" ]
  }
}
```

It is also possible to require uniqueness within an array of objects. For example, we can require that persons have different IDs with the @ symbol:

```
{
  "my-type" : {
    "field!" : [
      {
        "id@" : "integer",
        "first!" : "string",
        "last!" : "string=N/A",
        "age!" : "integer"
      }
    ]
  }
}
```

The following instance is then valid:

```
{
  "field" : [
    { "id" : 1, "first" : "James", "last" : "Kirk", "age" : 30 },
    { "id" : 2, "first" : "Kathryn", "last" : "Janeway", "age" : 50 },
    { "id" : 3, "first" : "Spock", "age" : 234 }
  ]
}
```

But not this one:

```
{
  "field" : [
    { "id" : 1, "first" : "James", "last" : "Kirk", "age" : 30 },
    { "id" : 2, "first" : "Kathryn", "last" : "Janeway", "age" : 50 },
    { "id" : 2, "first" : "Spock", "age" : 234 }
  ]
}
```
