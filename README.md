# Using vocabularies within the Transparency Information Language

This repository deals with custom vocabularies that can be used to further specify fields of tilt (Transparency Information Language and Toolkit). Refer here for the [main schema](https://github.com/Transparency-Information-Language/schema) or [here](https://github.com/Transparency-Information-Language) for all other specificities. 

# Why?

For many use cases, there is a central requirement of structured purposes for which personal data may be used. This structure can be in the form of a hierarchy (e.g. tree) or as a directed [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph). This practice may be useful for different purposes depending on how “broad“ or “narrow“ respectively transparency should be guaranteed. An example for such a hierachy might be the following:

1. Research / Marketing / ...
2. Clinical research / Advertising / ...
3. COVID19 research / Targetet advertsing / ....
4. Polymerase chain reaction testing / Tracking technologies including mouse movements / ...

# Usage

Given the `purposeConditional` field should be not only based on the form of an `string` typed `array`, but with several allowed or disallowed `string` values.

1. Create a json schema for the `purpose` field specifiying all values or rules (`tilt-purpose-vocabulary.schema.json`).

```json
{
    "$schema":"http://json-schema.org/draft-07/schema#",
    "type":"object",
    "properties": {
        "purpose": {
            "$ref":"#/definitions/purpose"
        }
    },
    "required": [
        "purpose"
    ],
    "definitions": {
        "purpose": {
            "type":"string",
            "allOf": [
                {
                    "anyOf": [
                        {
                            "const": "Apple"
                        },
                        {
                            "enum": [
                                "Cat",
                                "Dog"
                            ]
                        }
                    ]
                },
                {
                    "not":{
                        "enum":[
                            "Earth",
                            "Water",
                            "Air",
                            "Fire"
                        ]
                    }
                }
            ]
        }
    }
}
```

2. Change the items specification within the `tilt-schema.json` to the url where your `tilt-purpose-vocabulary.schema.json` file lives.

```json
"purposeConditional":{
  "$id":"#/properties/dataDisclosed/items/anyOf/0/properties/storage/items/anyOf/0/properties/purposeConditional",
    "type":"array",
    "title":"PurposeConditional",
    "description":"Specifies the purpose that requires data storage.",
    "examples":[
        [
            "Data is stored until the end of the ordering process."
        ]
    ],
    "additionalItems":true,
    "items":{
        "anyOf":[
            {
                "$ref":"https://<your-url>/tilt-purpose-vocabulary.schema.json#/definitions/purpose",
                "title":"AnyOf schema",
                "examples":[
                    "Data is stored until the end of the ordering process."
                ]
            }
        ],
      "$id":"#/properties/dataDisclosed/items/anyOf/0/properties/storage/items/anyOf/0/properties/purposeConditional/items"
    }
},
```



3. Validate your examples, e.g. using [python-tilt](https://github.com/Transparency-Information-Language/python-tilt) or [java-tilt](https://github.com/Transparency-Information-Language/java-tilt).