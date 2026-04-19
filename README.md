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
      "description": "Referencing Zarr objects external to the current Zarr object"
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
| node       | string | Path to a group or array in the store.      | Yes |
| attribute  | string | JSON pointer to an attribute of the array or group. | No       |

#### uri
The Uniform Resource Identifier of an external Zarr store, compliant with [RFC 3986](https://www.rfc-editor.org/rfc/rfc3986). It is recommended to use a locatable URI for a Zarr store that has the same level of accessibility as the referencing Zarr store.

This field MUST NOT be provided if the referenced node is located in the current Zarr store.

#### node
Path to a referenced node, either in the current store or in the store identified by the `uri` field. If the node is in the current store, it is a relative path from the referencing node. If the node is in a Zarr store identified by the `uri` field, the path is absolute from the root of the referenced Zarr store.

#### attribute
JSON pointer to an item in the `zarr.json` file of the referenced node. The JSON pointer MUST follow the format described in [RFC 6901](https://datatracker.ietf.org/doc/html/rfc6901). The JSON pointer MUST therefore be fully-qualified and start from the root of the `zarr.json` file of the referenced node, for example `"attribute": "/attributes/object/property"`.

## Examples

#### Reference to a sibling array in the current store 
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
      "node": "../sibling_array"
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
      "node": "../parent_group",
      "attribute": "/attributes/interesting_thing"
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
      "node": "/year"
    }
  }
}
```

## Known Implementations

_If you implement or use this convention, please add your implementation to this list by opening an issue or submitting a pull request._

## Acknowledgements

This template is based on the [STAC extensions template](https://github.com/stac-extensions/template/blob/main/README.md).
