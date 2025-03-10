---
title: "object"
section: docs
prev: 
  label: Numeric Types
  url: /understanding-json-schema/reference/numeric
next: 
  label: Regular expressions
  url: /understanding-json-schema/reference/regular_expressions
---

<Keywords label="single: object" />

Objects are the mapping type in JSON. They map \"keys\" to \"values\".
In JSON, the \"keys\" must always be strings. Each of these pairs is
conventionally referred to as a \"property\".

<Keywords label="single: object; properties single: properties" />

[tabs-start "Language-specific info"]

[tab "Python"]
In Python, "objects" are analogous to the `dict` type.  An
important difference, however, is that while Python dictionaries
may use anything hashable as a key, in JSON all the keys
must be strings.

Try not to be confused by the two uses of the word "object" here:
Python uses the word `object` to mean the generic base class for
everything, whereas in JSON it is used only to mean a mapping from
string keys to values.

[tab "Ruby"]
In Ruby, "objects" are analogous to the `Hash` type. An important
difference, however, is that all keys in JSON must be strings, and therefore
any non-string keys are converted over to their string representation.

Try not to be confused by the two uses of the word "object" here:
Ruby uses the word `Object` to mean the generic base class for
everything, whereas in JSON it is used only to mean a mapping from
string keys to values.

[tab "Objective-C"]
In Objective-C, "objects" are analogous to the `NSDictionary` type.

[tab "Swift"]
In Swift, "objects" are analogous to the `Dictionary` type.

[tabs-end]

```json
// props { "isSchema": true }
{ "type": "object" }
```
```json
// props { "indent": true, "valid": true }
{
  "key": "value",
  "another_key": "another_value"
}
```
```json
// props { "indent": true, "valid": true }
{
  "Sun": 1.9891e30,
  "Jupiter": 1.8986e27,
  "Saturn": 5.6846e26,
  "Neptune": 10.243e25,
  "Uranus": 8.6810e25,
  "Earth": 5.9736e24,
  "Venus": 4.8685e24,
  "Mars": 6.4185e23,
  "Mercury": 3.3022e23,
  "Moon": 7.349e22,
  "Pluto": 1.25e22
}
```
Using non-strings as keys is invalid JSON:

```json
// props { "indent": true, "valid": false }
{
  0.01: "cm",
  1: "m",
  1000: "km"
}
```
```json
// props { "indent": true, "valid": false }
"Not an object"
```
```json
// props { "indent": true, "valid": false }
["An", "array", "not", "an", "object"]
```

## Properties

The properties (key-value pairs) on an object are defined using the
`properties` [keyword](../../learn/glossary#keyword). The value of `properties` is an object, where each
key is the name of a property and each value is a [schema](../../learn/glossary#schema) used to
validate that property. Any property that doesn\'t match any of the
property names in the `properties` keyword is ignored by this keyword.

> See [Additional Properties](#additionalproperties) and
[Unevaluated Properties](#unevaluatedproperties) for how to disallow properties that
don\'t match any of the property names in `properties`.

For example, let\'s say we want to define a simple schema for an address
made up of a number, street name and street type:

```json
// props { "isSchema": true }
{
  "type": "object",
  "properties": {
    "number": { "type": "number" },
    "street_name": { "type": "string" },
    "street_type": { "enum": ["Street", "Avenue", "Boulevard"] }
  }
}
```
```json
// props { "indent": true, "valid": true }
{ "number": 1600, "street_name": "Pennsylvania", "street_type": "Avenue" }
```
// If we provide the number in the wrong type, it is invalid:

```json
// props { "indent": true, "valid": false }
{ "number": "1600", "street_name": "Pennsylvania", "street_type": "Avenue" }
```
By default, leaving out properties is valid. See [Required Properties](#required).

```json
// props { "indent": true, "valid": true }
{ "number": 1600, "street_name": "Pennsylvania" }
```
By extension, even an empty object is valid:

```json
// props { "indent": true, "valid": true }
{ }
```
By default, providing additional properties is valid:

```json
// props { "indent": true, "valid": true }
{ "number": 1600, "street_name": "Pennsylvania", "street_type": "Avenue", "direction": "NW" }
```

## Pattern Properties [#patternProperties]

Sometimes you want to say that, given a particular kind of property
name, the value should match a particular schema. That\'s where
`patternProperties` comes in: it maps regular expressions to schemas. If
a property name matches the given regular expression, the property value
must validate against the corresponding schema.

> Regular expressions are not anchored. This means that when defining the
regular expressions for `patternProperties`, it\'s important to note
that the expression may match anywhere within the property name. For
example, the regular expression `"p"` will match any property name with
a `p` in it, such as `"apple"`, not just a property whose name is simply
`"p"`. It\'s therefore usually less confusing to surround the regular
expression in `^...$`, for example, `"^p$"`.


In this example, any properties whose names start with the prefix `S_`
must be strings, and any with the prefix `I_` must be integers. Any
properties that do not match either regular expression are ignored.

```json
// props { "isSchema": true }
{
  "type": "object",
  "patternProperties": {
    "^S_": { "type": "string" },
    "^I_": { "type": "integer" }
  }
}
```
```json
// props { "indent": true, "valid": true }
{ "S_25": "This is a string" }
```
```json
// props { "indent": true, "valid": true }
{ "I_0": 42 }
```
If the name starts with `S_`, it must be a string

```json
// props { "indent": true, "valid": false }
{ "S_0": 42 }
```
If the name starts with `I_`, it must be an integer

```json
// props { "indent": true, "valid": false }
{ "I_42": "This is a string" }
```
This is a key that doesn't match any of the regular expressions:

```json
// props { "indent": true, "valid": true }
{ "keyword": "value" }
```

## Additional Properties [#additionalproperties]

The `additionalProperties` keyword is used to control the handling of
extra stuff, that is, properties whose names are not listed in the
`properties` keyword or match any of the regular expressions in the
`patternProperties` keyword. By default any additional properties are
allowed.

The value of the `additionalProperties` keyword is a schema that will be
used to validate any properties in the [instance](../../learn/glossary#instance) that are not matched by
`properties` or `patternProperties`. Setting the `additionalProperties`
schema to `false` means no additional properties will be allowed.

Reusing the example from [Properties](#properties), but this time setting
`additionalProperties` to `false`.

```json
// props { "isSchema": true }
{
  "type": "object",
  "properties": {
    "number": { "type": "number" },
    "street_name": { "type": "string" },
    "street_type": { "enum": ["Street", "Avenue", "Boulevard"] }
  },
  "additionalProperties": false
}
```
```json
// props { "indent": true, "valid": true }
{ "number": 1600, "street_name": "Pennsylvania", "street_type": "Avenue" }
```
Since `additionalProperties` is `false`, this extra property "direction" makes the object invalid:

```json
// props { "indent": true, "valid": false }
{ "number": 1600, "street_name": "Pennsylvania", "street_type": "Avenue", "direction": "NW" }
```

You can use non-boolean schemas to put more complex constraints on the
additional properties of an instance. For example, one can allow
additional properties, but only if their values are each a string:

```json
// props { "isSchema": true }
{
  "type": "object",
  "properties": {
    "number": { "type": "number" },
    "street_name": { "type": "string" },
    "street_type": { "enum": ["Street", "Avenue", "Boulevard"] }
  },
  "additionalProperties": { "type": "string" }
}
```
```json
// props { "indent": true, "valid": true }
{ "number": 1600, "street_name": "Pennsylvania", "street_type": "Avenue" }
```
This is valid, since the additional property's value is a string:

```json
// props { "indent": true, "valid": true }
{ "number": 1600, "street_name": "Pennsylvania", "street_type": "Avenue", "direction": "NW" }
```
This is invalid, since the additional property's value is not a string:

```json
// props { "indent": true, "valid": false }
{ "number": 1600, "street_name": "Pennsylvania", "street_type": "Avenue", "office_number": 201 }
```

You can use `additionalProperties` with a combination of `properties`
and `patternProperties`. In the following example, based on the example
from [patternProperties](#patternProperties), we add a `"builtin"` property,
which must be a number, and declare that all additional properties (that
are neither defined by `properties` nor matched by `patternProperties`)
must be strings:

<Keywords label="single: object; properties; additionalProperties single: extending" />

```json
// props { "isSchema": true }
{
  "type": "object",
  "properties": {
    "builtin": { "type": "number" }
  },
  "patternProperties": {
    "^S_": { "type": "string" },
    "^I_": { "type": "integer" }
  },
  "additionalProperties": { "type": "string" }
}
```
```json
// props { "indent": true, "valid": true }
{ "builtin": 42 }
```
This is a key that doesn't match any of the regular expressions:

```json
// props { "indent": true, "valid": true }
{ "keyword": "value" }
```
It must be a string:

```json
// props { "indent": true, "valid": false }
{ "keyword": 42 }
```

### Extending Closed Schemas[#extending]

It\'s important to note that `additionalProperties` only recognizes
properties declared in the same [subschema](../../learn/glossary#subschema) as itself. So,
`additionalProperties` can restrict you from \"extending\" a schema
using [combining](../../understanding-json-schema/reference/combining) keywords such as [allOf](../../understanding-json-schema/reference/combining#allof). In
the following example, we can see how the `additionalProperties` can
cause attempts to extend the address schema example to fail.

```json
// props { "isSchema": true }
{
  "allOf": [
    {
      "type": "object",
      "properties": {
        "street_address": { "type": "string" },
        "city": { "type": "string" },
        "state": { "type": "string" }
      },
      "required": ["street_address", "city", "state"],
      "additionalProperties": false
    }
  ],

  "properties": {
    "type": { "enum": [ "residential", "business" ] }
  },
  "required": ["type"]
}
```
Fails `additionalProperties`. "type" is considered additional.

```json
// props { "indent": true, "valid": false }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC",
  "type": "business"
}
```
Fails `required`. "type" is required.

```json
// props { "indent": true, "valid": false }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC"
}
```

Because `additionalProperties` only recognizes properties declared in
the same subschema, it considers anything other than
\"street\_address\", \"city\", and \"state\" to be additional. Combining
the schemas with [allOf](../../understanding-json-schema/reference/combining#allof) doesn\'t change that. A workaround
you can use is to move `additionalProperties` to the extending schema
and redeclare the properties from the extended schema.

```json
// props { "isSchema": true }
{
  "allOf": [
    {
      "type": "object",
      "properties": {
        "street_address": { "type": "string" },
        "city": { "type": "string" },
        "state": { "type": "string" }
      },
      "required": ["street_address", "city", "state"]
    }
  ],

  "properties": {
    "street_address": true,
    "city": true,
    "state": true,
    "type": { "enum": [ "residential", "business" ] }
  },
  "required": ["type"],
  "additionalProperties": false
}
```
```json
// props { "indent": true, "valid": true }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC",
  "type": "business"
}
```
```json
// props { "indent": true, "valid": false }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC",
  "type": "business",
  "something that doesn't belong": "hi!"
}
```

Now the `additionalProperties` keyword is able to recognize all the
necessary properties and the schema works as expected. Keep reading to
see how the `unevaluatedProperties` keyword solves this problem without
needing to redeclare properties.

## Unevaluated Properties [#unevaluatedproperties]

<Star label="New in draft 2019-09" />

In the previous section we saw the challenges with using
`additionalProperties` when \"extending\" a schema using
[combining](../../understanding-json-schema/reference/combining). The `unevaluatedProperties` keyword is similar
to `additionalProperties` except that it can recognize properties
declared in subschemas. So, the example from the previous section can be
rewritten without the need to redeclare properties.

```json
// props { "isSchema": true }
{
  "allOf": [
    {
      "type": "object",
      "properties": {
        "street_address": { "type": "string" },
        "city": { "type": "string" },
        "state": { "type": "string" }
      },
      "required": ["street_address", "city", "state"]
    }
  ],

  "properties": {
    "type": { "enum": ["residential", "business"] }
  },
  "required": ["type"],
  "unevaluatedProperties": false
}
```
```json
// props { "indent": true, "valid": true }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC",
  "type": "business"
}
```
```json
// props { "indent": true, "valid": false }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC",
  "type": "business",
  "something that doesn't belong": "hi!"
}
```

`unevaluatedProperties` works by collecting any properties that are
successfully validated when processing the schemas and using those as
the allowed list of properties. This allows you to do more complex
things like conditionally adding properties. The following example
allows the \"department\" property only if the \"type\" of address is
\"business\".

```json
// props { "isSchema": true }
{
  "type": "object",
  "properties": {
    "street_address": { "type": "string" },
    "city": { "type": "string" },
    "state": { "type": "string" },
    "type": { "enum": ["residential", "business"] }
  },
  "required": ["street_address", "city", "state", "type"],

  "if": {
    "type": "object",
    "properties": {
      "type": { "const": "business" }
    },
    "required": ["type"]
  },
  "then": {
    "properties": {
      "department": { "type": "string" }
    }
  },

  "unevaluatedProperties": false
}
```
```json
// props { "indent": true, "valid": true }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC",
  "type": "business",
  "department": "HR"
}
```
```json
// props { "indent": true, "valid": false }
{
  "street_address": "1600 Pennsylvania Avenue NW",
  "city": "Washington",
  "state": "DC",
  "type": "residential",
  "department": "HR"
}
```

In this schema, the properties declared in the `then` schema only count
as \"evaluated\" properties if the \"type\" of the address is
\"business\".

<Keywords label="single: object; required properties single: required" />

## Required Properties[#required]

By default, the properties defined by the `properties` keyword are not
required. However, one can provide a list of required properties using
the `required` keyword.

The `required` keyword takes an array of zero or more strings. Each of
these strings must be unique.

<Infobox label="Draft-specific info">
In Draft 4, `required` must contain at least one string.
</Infobox>

In the following example schema defining a user record, we require that
each user has a name and e-mail address, but we don\'t mind if they
don\'t provide their address or telephone number:

<Keywords label="single: object; property names single: propertyNames" />

```json
// props { "isSchema": true }
{
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "email": { "type": "string" },
    "address": { "type": "string" },
    "telephone": { "type": "string" }
  },
  "required": ["name", "email"]
}
```
```json
// props { "indent": true, "valid": true }
{
  "name": "William Shakespeare",
  "email": "bill@stratford-upon-avon.co.uk"
}
```
Providing extra properties is fine, even properties not defined in the schema:

```json
// props { "indent": true, "valid": true }
{
  "name": "William Shakespeare",
  "email": "bill@stratford-upon-avon.co.uk",
  "address": "Henley Street, Stratford-upon-Avon, Warwickshire, England",
  "authorship": "in question"
}
```
Missing the required "email" property makes the JSON document invalid:

```json
// props { "indent": true, "valid": false }
{
  "name": "William Shakespeare",
  "address": "Henley Street, Stratford-upon-Avon, Warwickshire, England",
}
```
In JSON a property with value `null` is not equivalent to the property not being present. This fails because ``null`` is not of type "string", it's of type "null"

```json
// props { "indent": true, "valid": false }
{
  "name": "William Shakespeare",
  "address": "Henley Street, Stratford-upon-Avon, Warwickshire, England",
  "email": null
}
```

<Keywords label="single: object; property names single: propertyNames" />

## Property names [#propertyNames]

<Star label="New in draft 6" />

The names of properties can be validated against a schema, irrespective
of their values. This can be useful if you don\'t want to enforce
specific properties, but you want to make sure that the names of those
properties follow a specific convention. You might, for example, want to
enforce that all names are valid ASCII tokens so they can be used as
attributes in a particular programming language.

```json
// props { "isSchema": true }
{
  "type": "object",
  "propertyNames": {
    "pattern": "^[A-Za-z_][A-Za-z0-9_]*$"
  }
}
```
```json
// props { "indent": true, "valid": true }
{
  "_a_proper_token_001": "value"
}
```
```json
// props { "indent": true, "valid": false }
{
  "001 invalid": "value"
}
```

Since object keys must always be strings anyway, it is implied that the
schema given to `propertyNames` is always at least:

```json
// props { "isSchema": true }
{ "type": "string" }
```

<Keywords label="single: object; size single: minProperties single: maxProperties" />

## Size[#size]

The number of properties on an object can be restricted using the
`minProperties` and `maxProperties` keywords. Each of these must be a
non-negative integer.

```json
// props { "isSchema": true }
{
  "type": "object",
  "minProperties": 2,
  "maxProperties": 3
}
```
```json
// props { "indent": true, "valid": false }
{}
```
```json
// props { "indent": true, "valid": false }
{ "a": 0 }
```
```json
// props { "indent": true, "valid": true }
{ "a": 0, "b": 1 }
```
```json
// props { "indent": true, "valid": true }
{ "a": 0, "b": 1, "c": 2 }
```
```json
// props { "indent": true, "valid": false }
{ "a": 0, "b": 1, "c": 2, "d": 3 }
```