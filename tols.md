TOLS
====

Tom's Obvious Liuggio Scheme.

By Tom Preston-Werner, Giulio De Donato.

TOLS is like XSD, only more readable and simpler.

There are 3 simple rules to follow:

- A TOLS file is itself a valid TOML file.
- Each element could be validated.
- Explicit validation - if an element has not a validation scheme is valid by default.

Use cases
----------

Given a TOLS file, as developer I want to simple **create** a TOML file.

Given a TOLS file, as developer I want to **validate** an existant TOML file, having the proper **default** values.

Example
-------

```toml
# This is a TOLS document. Boom.
# This is also a TOML document. Boooom. (see rule n.1)

[title]                    # this is the name of the key
    [scheme]               # scheme is a keyword
    primitive = "String"   # the title must be a String
    required = true        #
    lenght.max = 254       # max string lenght (exclusive <)

[age]                      # this is the name of the key
    [scheme]
    primitive = "Integer"  # the age must be an Integer
    range.max = 100        # age is not required, but if the value is defined, should be maximum 100 (exclusive <)
    range.min = 17         # age is not required, but if the value is defined, should be minimum 17 (exclusive >)
```

The above validates the following two examples

```toml
title = "TOML Example"
age = 34
```

```toml
title = "TOML Example"
```

# Boolean example

```toml
# toml
enabled = true
```

```toml
# tols
[enabled]
    [scheme]
    primitive = "Boolean"      # Typing the enable value to Boolean
    default = true
#    required = false          # if omitted the required is false by default
```

# Integer, Float and Datetime example < Boolean

Integer, Float and Datetime share the same schema

```toml
# toml
age = 34
```

```toml
# tols
[age]
    [scheme]
    primitive = "Integer"       # Typing the value
    default = 190               # The default value for the current key
    required = false            # by default is false
    range.min = 0               # (exclusive <)
    range.max = 0               # (exclusive >)
```

# String example < Boolean

```toml
# tols

[email]
    [scheme]
    primitive = "String"            # typing
    required = true                 # default is false
#    default = "liuggio@gmail.com"  # the default value for the current key
    lenght.max = 254                # max string lenght (exclusive <)
    lenght.min = 5                  # min lenght (exclusive >)
    pattern= "^[A-Z0-9._%+-]+@[A-Z0-9.-]+\\.[A-Z]{2,4}$"   #regular expression
```

# Array

```toml
# toml file
data = [ ["gamma", "delta"], [1, 2] ]
```

Here the scheme

```toml
# tols

[data]
    [scheme]
    primitive = "Array"         # Typing
    lenght.max = 2              # max array lenght (exclusive <)
    lenght.min = 0              # min array lenght (exclusive >)
    content.primitive = "Array" # in toml you can't mix data types, so you could explicit the first nested content
```

Mmmm this is so easy, I want to validate also "gamma" and "delta" how could I do?


```toml
# tols
[data]
    [0.scheme]                   # this is the first element of data ["gamma", "delta"]
    primitive = "Array"          # is an Array
    lenght.max = 2               # max lenght (exclusive <)
    lenght.min = 0               # min lenght (exclusive >)
    content.primitive = "String" # the content should be a String
    content.lenght.max = 254     # content takes the String Scheme Behaviour
#    content.pattern = /?/       # regular expr

    [1.scheme]                   # we are validating the [1, 2]
    primitive = "Array"          # should be an array
    lenght.max = 2               # max string lenght (exclusive <)
    lenght.min = 0               # min lenght (exclusive >)
    content.primitive = "Integer"# content takes the Integer Scheme Behaviour
    content.range.min = 0
    content.range.max = 10
```

# Hash

```toml
# toml

[fruit.type]
apple = "yes"
orange = "no"
```

```toml
# tols
[fruit.type.apple.scheme]
primitive = "String"
required = true
lenght.max = 3
```

or just put the validation in the apple.type content

```toml
# tols
[fruit.type.scheme]
primitive = "Hash"
required = true
lenght.max = 3
content.primitive = "String"
content.required = true
content.lenght.max = 3
```

All the keywords
-------

TOLS schema has 8 keywords.

- **schema** is an Hash and describes the behaviour for a defined field.

- **primitive** could be: ["String", "Integer", "Float", "Boolean", "Datetime", "Array", "Hash"].

- **default** contains the default value for the current field.

- **required** is a Boolean value, by default is false.

- **lenght** is a hash that contains two values **min** and **max**, only when primitives are [String, Array, Hash].

- **range** is a hash that contains two values **min** and **max**, only when primitives are [Integer, Float, Datetime].

- **pattern** is a String and contains a valid Regular Expression only when primitives is a String.

- **content** is a Hash and describes the behaviour of the first nested element.

TOLS is a valid TOML file, could I validate a TOLS file?
-------

Don't drink too much man.
