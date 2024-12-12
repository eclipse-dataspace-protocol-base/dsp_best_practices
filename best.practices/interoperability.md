# Interoperability Requirements

The normative JSON schemas are very permissive. Adding `additionalProperties` is always allowed as long as they satisfy
the requirements of JSON-LD. Dataspaces are encouraged to define normative JSON schemas extending the list of required
and optional properties from the DSP JSON schemas.

## Additional Properties on Datasets

To allow a `Data Consumer` to negotiate only for relevant `odrl:Set`s, a `Data Provider` should attach meaningful
metadata about the `dcat:Dataset`. They may serve as discriminator for `Consumer`s to expect a certain internal
structure of the Dataset. For example, the navigatable URL-segments of an HTTPS based API may be signified by a
predetermined property.

## Participant Identification

Various DSP messages include an identifier for the participants, like `providerId`, `consumerId` and `participantId`.
Dataspaces may have conventions for these properties, for instance as regexes.

## Filtering

The `CatalogRequestMessage` defines a `filter` property that holds a generic json object. Dataspaces are encouraged to 
restrict that object to a well-defined structure allowing  `Data Consumers` to signify the kind of `Datasets` or `Sets`
they are interested in. `Data Providers` shall restrict the returned `Catalog` to content that matches the filter
according to agreed semantics.

## Data Transfer

To cover the data transfer, a Dataspace must agree on a predefined set of transport protocols and define how they are signalled in
the relevant DSP messages. The following properties should be considered when restricting data transfers:
- `Catalog.dataset.distribution.format`, 
- `TransferRequestMessage.dataAddress.endpointType`, 
- `TransferRequestMessage.format`
- `TransferStartMessage.dataAddress.endpointType`

There must be shared knowledge between `Provider` and `Consumer` how to trigger a data transfer based on the 
set of `dataAddress.endpointProperties`.