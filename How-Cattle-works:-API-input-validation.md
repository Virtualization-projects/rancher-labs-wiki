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

## Validation filter

Obviously the schema validation won't be good enough for more complex validation requirement, so there are something can be done with code logic can help. That's we called *Validation Filter*. The filter are defined in using _spring-iaas-api-logic-context.xml_, like following:

    <bean class="io.cattle.platform.iaas.api.filter.instance.InstanceImageValidationFilter" />
    <bean class="io.cattle.platform.iaas.api.filter.instance.InstanceNetworkValidationFilter" />
    <bean class="io.cattle.platform.iaas.api.filter.instance.InstancePortsValidationFilter" />

The filter implemented should extends _AbstractResourceManagerFilter_. _AbstractDefaultResourceManagerFilter_ would set priority to Priority.DEFAULT on top of that.

The main functions in the _AbstractResourceManagerFilter_ are:

* public String[] getTypes(): define which type of resource(say, subtype of resource) you want to filer on.
* public Class<?>[] getTypeClasses(): define which resource you want to filter on. Notice it wouldn't cover the subtype of this resource type.
* create()/update()/delete(): Place to hook in filter logic.