# Filtering

The Fuel Rats API allows you to filter search queries through the filter query parameter.
For HTTP queries this is a URL encoded JSON object passed as the ?filter query parameter, for Websocket queries this is an object named 'filter' in the top level of the websocket request.

### Supported Operators

#### and
Display results where all in a list of conditions are met. Allows nested usage with other operators.

```json
{ "and": [{ "name": "Absolver" }, { "platform": "pc" }] }
```


#### or
Display results where at least one in a list of conditions are met. Allows nested usage with other operators.

```json
{ "or": [{ "name": "Absolver" }, { "name": "Arathenes" }] }
```
```json
{ "rescueCount": { "or": { "lt": 10, "gt": 100 } } }
```


#### gt
Display results where the value is greater than the given value.

```json
{ "rescueCount": { "gt": 3 } }
```


#### gte
Display results where the value is greater than or equal to the given value.

```json
{ "rescueCount": { "gte": 4 } }
```


#### lt
Display results where the value is less than the given value.

```json
{ "rescueCount": { "lt": 11 } }
```


#### lte
Display results where the value is less than or equal to the given value.

```json
{ "rescueCount": { "lte": 10 } }
```


#### eq
Display results where the value is the same as the exact value provided. Does not work for NULL, for that see the is operator.

```json
{ "name": { "eq": "NumberPi" } }
```


#### ne
Display results where value is anything except the exact value provided. Does not work for NULL, for that see the not operator.

```json
{ "name": { "ne": "NumberTau" } }
```


#### is
Operator used for checking whether something is null, this exists sperately from the equality operators do to the behaviour of the back-end database.

```json
{ "client": { "is": null } }
```


#### not
Negates the query within it.

```json
{ "client": { "not": null } }
```
```json
{ "outcome": { "not": "success" } }
```


#### in / any
Displays results where the item is within an array of provided values.

```json
{ "outcome": { "any": ["invalid", "other"] } }
```

```json
{ "outcome": { "any": ["invalid", "other"] } }
```


#### notIn
Dispalys results where the item is NOT within an array of provided values.

```json
{ "system": { "notIn": ["NLTT 48288", "MCC 811", "ASHIMA"] } }
```


#### like
Displays results where the item matches a provided wildcard expression (% is the wildcard operator).

```json
{ "system": { "like": "ALRAI SECTOR %" } }
```


#### notLike
Displays results where the item does not match a provided wildcard expression (% is the wildcard operator).

```json
{ "system": { "notLike": "ALRAI SECTOR %" } }
```


#### ilike
Displays results where the item matches a provided wildcard expression (% is the wildcard operator). Case insensitive.

```json
{ "system": { "like": "alrai sector %" } }
```


#### notIlike
Displays results where the item does not match a provided wildcard expression (% is the wildcard operator). Case insensitive.

```json
{ "system": { "like": "alrai sector %" } }
```


#### startsWith
Displays results where the item matches is a string that starts with the provided value.

```json
{ "name": { "startsWith": "Number" } }
```


#### endsWith
Displays results where the item matches is a string that ends with the provided value.

```json
{ "name": { "startsWith": "Pi" } }
```


#### substring
Displays results where the item matches is a string that has the provided value somewhere within it.

```json
{ "system": { "substring": "sector" } }
```


#### regexp
Displays results that matches a provided regular expression.

```json
{ "name": { "regexp": "\b(num(ber)?|ninja)?(?<!rasp( |-))(?<!raspberry )(pi(-?rat)?(?!-| dhod| dimo| dimshi| disci| hydrae| mensae| pavonis| piscis| sculptoris| piscium))'?s?\b" } }
```

#### notRegexp
Displays results that does NOT match a provided regular expression.

```json
{ "name": { "notRegexp": "\b(num(ber)?|ninja)?(?<!rasp( |-))(?<!raspberry )(pi(-?rat)?(?!-| dhod| dimo| dimshi| disci| hydrae| mensae| pavonis| piscis| sculptoris| piscium))'?s?\b" } }
```

#### iRegexp
Displays results that matches a provided regular expression (case insensitive).

```json
{ "name": { "iRegexp": "\b(num(ber)?|ninja)?(?<!rasp( |-))(?<!raspberry )(pi(-?rat)?(?!-| dhod| dimo| dimshi| disci| hydrae| mensae| pavonis| piscis| sculptoris| piscium))'?s?\b" } }
```

#### notIRegexp
Displays results that does NOT match a provided regular expression (case insensitive).

```json
{ "name": { "notIRegexp": "\b(num(ber)?|ninja)?(?<!rasp( |-))(?<!raspberry )(pi(-?rat)?(?!-| dhod| dimo| dimshi| disci| hydrae| mensae| pavonis| piscis| sculptoris| piscium))'?s?\b" } }
```

#### contains
Displays results with an array that contains the provided range of values somewhere within it.

```json
{ "permissions": { "contains": ["rescue.write", "rescue.read"] } }
```

#### contained
Displays results with an array that is contained by the provided range of values.

```json
{ "permissions": { "contained": ["rescue.write", "rat.write","rescue.delete", "user.read"] } }
```

#### overlap
Displays results with an array that has overlap with the provided range of values.

```json
{ "permissions": { "contained": ["rescue.write", "rat.write","rescue.delete", "user.read"] } }
```
