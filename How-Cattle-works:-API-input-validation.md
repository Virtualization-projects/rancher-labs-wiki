## Overview

This page would describe how API validation works in Cattle.

There are two levels of API validation in Cattle, schema level and logic level.

## Schema validation

Schema validation can be defined in *resource_name.json*, for example, as you can see in *subnetIpPool.json* defined maxLength of field _gateway_.

>     "resourceFields": {
>         "gateway": {
>             "default": null,
>             "options": null,
>             "maxLength": 255,
>             "minLength": null,
>             "max": null,
>             "min": null,
>             "type": "string",
>             "validChars": null,
>             "invalidChars": null,
>             "create": true,
>             "update": false,
>             "nullable": true,
>             "unique": false,
>             "required": false
>         },

The full list of available options are at: [API spec](https://github.com/rancherio/api-spec)

