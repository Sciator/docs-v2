---
title: duplicate() function
description: The `duplicate()` function duplicates a specified column in a table.
aliases:
  - /v2.0/reference/flux/functions/transformations/duplicate
menu:
  v2_0_ref:
    name: duplicate
    parent: built-in-transformations
weight: 401
---

The `duplicate()` function duplicates a specified column in a table.
If the specified column is part of the group key, it will be duplicated, but will
not be part of the output table's group key.

_**Function type:** Transformation_  
_**Output data type:** Object_

```js
duplicate(column: "column-name", as: "duplicate-name")
```

## Parameters

### column
The column to duplicate.

_**Data type:** String_

### as
The name assigned to the duplicate column.

_**Data type:** String_

## Examples
```js
from(bucket: "telegraf/autogen")
	|> range(start:-5m)
	|> filter(fn: (r) => r._measurement == "cpu")
	|> duplicate(column: "host", as: "server")
```