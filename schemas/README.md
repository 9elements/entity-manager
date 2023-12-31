# Entity manager configuration file schema

Entity manager configuration files are JSON documents and can be validated with
a [JSON schema][1]. This document provides an overview of the general structure
of an entity manager configuration file.

An entity manager configuration file consists of one or more entity manager
configurations. This provides flexibility for system designers - the
configuration data for an entire platform or set of platforms can be expressed
in a single file, or can be organized into several smaller files. Thus, the top
most schema describes a single object or an array of objects.

To remain consistent with the OpenBMC CPP source code file name guidelines,
entity manager configuration file and schema file names must be
`lower_snake_case`.

## A single entity manager configuration

At its core entity manager simply exports objects onto DBus when a specific
event occurs. As such, an entity manager configuration provides a syntax for
describing the dbus interfaces to export, the object paths on which to add the
interfaces and the event that should trigger the export. This is accomplished
with four properties.

## Type

The type property identifies the type of the configuration. When exported,
configuration data will be instantiated as grandchildren of
`/xyz/openbmc_project/inventory/system/<Type>`. For a comprehensive list of
supported types, consult the schema (global.json).

## Name

The name property identifies the name of the configuration. When exported,
configuration data will be instantiated as children of
`/xyz/openbmc_project/inventory/system/<Type>/<Name>`. Additionally, any DBus
interfaces listed in openbmc-dbus.json will be added on
`/xyz/openbmc_project/inventory/system/<Type>/<Name>`.

## Probe

The probe property describes the event which will cause entity manager to export
the configuration to DBus, making it available to other applications.

## Exposes

The exposes property describes the configuration data at its most specific
level. The exposes property is an array of heterogeneous configuration data.
Each exposes element has its own schema which is outlined by the subschemas in
the schemas directory. Each schema must require a type and name (not to be
confused with the Type and Name properties of the parent JSON object) property
since entity manager will construct the Dbus interface name and object path
based on those properties:

`/xyz/openbmc_project/inventory/system/Board/RiserCard1/<Name>`:
`xyz.openbmc_project.Configuration.<Type>`:
`<remaining properties defined by exposes subschema>`

## Adding new exposes property subschema

New subschema for the exposes property must be added to the schema directory,
and then referenced from global.json at /definitions/EMExposesElement. Exposes
element subschema must require a type and name property (the validator will fail
otherwise) and should explicitly enumerate all properties and use
additionalProperties so mistakes can be found during validation.

## Adding new Dbus interfaces

To export new interfaces on the configuration parent object e.g.:
/xyz/openbmc_project/inventory/system/Board/RiserCard1 add additional subschema
to openbmc-dbus.json.

[1]: https://json-schema.org/
