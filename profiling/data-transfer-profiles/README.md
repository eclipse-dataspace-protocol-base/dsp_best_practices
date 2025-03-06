# Data Transfer Profiles

<!-- TOC -->
* [Data Transfer Profiles](#data-transfer-profiles)
  * [Data Transfer Protocol](#data-transfer-protocol)
    * [Catalog Response](#catalog-response)
    * [Transfer Request](#transfer-request)
    * [DataAddress](#dataaddress)
  * [Securing access to `Dataset`s](#securing-access-to-datasets)
  * [Additional Properties on Datasets](#additional-properties-on-datasets)
  * [Filtering](#filtering)
<!-- TOC -->

## Data Transfer Protocol

To cover the data transfer, a Dataspace must agree on a predefined set of transport protocols and define how they are
signalled in the relevant DSP messages. For each transport protocol, the following properties should be considered when
further specifying data transfers:

### Catalog Response

As per DSP, the Data Provider may answer to a [`CatalogRequestMessage`](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#catalog-request-message)
with a [`Catalog - ACK`](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#ack-catalog) message.
Each `Dataset` should include a `Distribution` with a `format` property. This property has a semantic definition in
the [DC terms vocabulary](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/#format). In the context of the
DSP, it's used to indicate the wire protocol (`HttpData` in this case) as well as the [Transfer Process Type](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#data-transfer-types)
(either push or pull). This property must be specified more precisely by a `Data Transfer Profile`. Please check the
`Catalog.dataset.distribution.format` property in the following abbreviated example:

```json
{
  "@context": [
    "https://w3id.org/dspace/2025/1/context.json"
  ],
  "@id": "urn:uuid:3afeadd8-ed2d-569e-d634-8394a8836d57",
  "@type": "Catalog",
  "participantId": "urn:example:DataProviderA",
  "dataset": [
    {
      "@id": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
      "@type": "Dataset",
      "distribution": [
        {
          "@type": "Distribution",
          "format": "example:HTTP_PULL",
          "accessService": "urn:uuid:4aa2dcc8-4d2d-569e-d634-8394a8834d77"
        }
      ]
    }
  ],
  "service": [
    {
      "@id": "urn:uuid:4aa2dcc8-4d2d-569e-d634-8394a8834d77",
      "@type": "DataService",
      "endpointURL": "https://provider-a.com/connector"
    }
  ]
}
```

### Transfer Request

After successful retrieval of the Catalog and subsequent Contract Negotiation, the Consumer initiates the Data Transfer
with a [`TransferRequestMessage`](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#transfer-request-message).
It includes the property `format` that the Consumer replays directly from the appropriate Dataset entry.

```json
{
  "@context": [
    "https://w3id.org/dspace/2025/1/context.json"
  ],
  "@type": "TransferRequestMessage",
  "consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "agreementId": "urn:uuid:e8dc8655-44c2-46ef-b701-4cffdc2faa44",
  "format": "example:HTTP_PULL",
  "callbackAddress": "https://example.com/callback"
}
```

### DataAddress

Depending on the [Transfer Process Type](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#data-transfer-types),
either the `TransferRequestMessage` (in case of push) or the `TransferStartMessage` (in case of pull) contain a
`dataAddress` property holding a `DataAddress` object. This object has an `endpointType` property that is filled by the
sender with the type of endpoint. It determines the set of required and optional `name`s in the `endpointProperties`
array. For a pull transfer, the permissible `dataAddress` property should be populated based on the advertised `format` 
for the Dataset. 

```json
{
    "@type": "DataAddress",
    "endpointType": "https://w3id.org/idsa/v4.1/HTTP",
    "endpoint": "http://example.com",
    "endpointProperties": [
      {
        "@type": "EndpointProperty",
        "name": "authorization",
        "value": "TOKEN-ABCDEFG"
      },
      {
        "@type": "EndpointProperty",
        "name": "authType",
        "value": "bearer"
      }
    ]
  }
```

`format`, `endpoint`, `endpointType` and the corresponding set of `endpointProperties` are unspecified by the DSP but 
must be further defined for each Data Transfer Protocol.

## Securing access to `Dataset`s

Granting access to data, i.e., the data transaction, is out of scope of the DSP specification. However, the DSP is a
vehicle to exchange the necessary information to grant access to such data. The result of successful the DSP
interactions is the transfer of a [`DataAddress` object](interoperability.md#dataaddress). This object declares the
means of transferring the business data and may include (usually short-lived) authorization material in the
`endpointProperties` array. The set of `EndpointProperty` objects needed to authorize must be specified for each
[Data Format](interoperability.md#data-formats) and shared between Consumer and Provider.

## Additional Properties on Datasets

To allow a Data Consumer to negotiate only for relevant `odrl:Set`s, a Data Provider should attach meaningful
metadata about the `dcat:Dataset`. They may serve as discriminator for Consumers to expect a certain internal
structure of the Dataset. For example, the navigatable URL-segments of an HTTPS based API may be signified by a
predetermined property.

## Filtering

The `CatalogRequestMessage` defines a `filter` property that holds a generic json object. Dataspaces are encouraged to
restrict that object to a well-defined structure allowing  `Data Consumers` to signify the kind of `Datasets` or `Sets`
they are interested in. `Data Providers` shall restrict the returned `Catalog` to content that matches the filter
according to agreed semantics.