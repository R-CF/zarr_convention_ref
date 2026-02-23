# External Reference Convention

- **UUID**: d89b30cf-ed8c-43d5-9a16-b492f0cd8786
- **Name**: "ref"
- **Schema URL**: "https://raw.githubusercontent.com/R-CF/zarr_convention_ref/main/schema.json"
- **Spec URL**: "https://raw.githubusercontent.com/R-CF/zarr_convention_ref/main/README.md"
- **Scope**: Array, Group
- **Extension Maturity Classification**: Proposal
- **Owner**: @pvanlaake

## Description

This convention defines a mechanism to reference external objects from a referencing array or group. "External" means that the referenced object is defined in another group or array, either in the store of the referencing group or array or in another store. The referenced object may be an array, a group, or an item in a `zarr.json` file.

The interpretation of the referenced object is application-specific and not defined in this convention.

## Motivation

- Uniform description of Zarr objects defined externally to the referencing Zarr object.
- External referencing allows for the efficient decomposition of complex objects, avoiding repetitive definition.

## Convention Registration
The convention must be registered in `zarr_conventions`:

```json
{
  "zarr_conventions": [
    {
      "schema_url": "https://raw.githubusercontent.com/R-CF/zarr_convention_ref/main/schema.json",
      "spec_url": "https://raw.githubusercontent.com/R-CF/zarr_convention_ref/main/README.md",
      "uuid": "d89b30cf-ed8c-43d5-9a16-b492f0cd8786",
      "name": "ref",
      "description": "External referencing of Zarr objects"
    }
  ]
}
```

## Application
This convention can be used with these parts of the Zarr hierarchy:

- [x] Group
- [x] Array

## Properties
The property is placed at the location in the attributes of the referencing Zarr object where the referenced object is required.

| Field Name | Type                      | Description                      | Required    |
| ---------- | ------------------------- | -------------------------------- | ----------- |
| ref        | [Ref object](#ref-object) | Reference to an external object. | Yes         |

### Ref object
The `ref` object defines all the properties of a reference to an external object.

| Field Name | Type   | Description                                 | Required |
| ---------- | ------ | ------------------------------------------- | -------- |
| uri        | string | URI to another store.                       | No       |
| array      | string | Path to an array in the store.              | Conditional |
| group      | string | Path to a group in the store.               | Conditional |
| attribute  | string | Name of an attribute of the array or group. | No       |
| index      | integer | Index of an element in `"attribute"`        | No |
| name       | string | Name of an attribute `"name"` in `"attribute"`. | No |

Only one of `group` or `array` MUST be provided. `uri` MUST NOT be provided if the referenced object is located in the current store.

#### uri
The Uniform Resource Identifier of an external Zarr store, compliant with [RFC 3986](https://www.rfc-editor.org/rfc/rfc3986). It is recommended to use a locatable URI for Zarr stores that are publicly accessible.

This field MUST NOT be provided if the referenced object is located in the current Zarr store.

#### array
Path to a referenced array, either in the current store or in the store identified by the `uri` field.

#### group
Path to a referenced group, either in the current store or in the store identified by the `uri` field.

#### attribute
Name of an item in the `zarr.json` file of the referenced `group` or `array`. The `"attribute"` value must be fully-qualified if the referenced attribute is nested, for example `"attribute": "attributes/object/property"`.

#### index
If `"attribute"` is a JSON array, the 0-based index of the element to retrieve.

#### name
If `"attribute"` is a JSON array of composite JSON sub-schemas, retrieve the sub-schema whose `"name"` element has the value given.

Fields `"index"` and `"name"` MUST NOT both be specified. Either one of the fields MAY only be specified if field `"attribute"` is specified and refers to a JSON array.

## Examples

#### Reference to an array in the current store 
```
{
  "attributes": {
    "zarr_conventions": [
      {
        "name": "ref",
        "schema_url": "https://raw.githubusercontent.com/R-CF/zarr_convention_ref/main/schema.json",
        "uuid": "d89b30cf-ed8c-43d5-9a16-b492f0cd8786"
      }
    ],
    "ref": {
      "array": "/path/to/array"
    }
  }
}
```

#### Reference to an attribute in a group in the current store 
```
{
  "attributes": {
    "zarr_conventions": [
      {
        "name": "ref",
        "schema_url": "https://raw.githubusercontent.com/R-CF/zarr_convention_ref/main/schema.json",
        "uuid": "d89b30cf-ed8c-43d5-9a16-b492f0cd8786"
      }
    ],
    "ref": {
      "group": "/path/to/group",
      "attribute": "attributes/interesting_thing"
    }
  }
}
```

#### Reference to an array in a different store 
```
{
  "attributes": {
    "zarr_conventions": [
      {
        "name": "ref",
        "schema_url": "https://raw.githubusercontent.com/R-CF/zarr_convention_ref/main/schema.json",
        "uuid": "d89b30cf-ed8c-43d5-9a16-b492f0cd8786"
      }
    ],
    "ref": {
      "uri": "https://data.earthdatahub.destine.eu/public/test-dataset-v0.zarr",
      "array": "/year"
    }
  }
}
```

## Known Implementations

_If you implement or use this convention, please add your implementation to this list by opening an issue or submitting a pull request._

## Acknowledgements

This template is based on the [STAC extensions template](https://github.com/stac-extensions/template/blob/main/README.md).
