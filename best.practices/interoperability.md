# Interoperability Requirements

The normative JSON schemas are very permissive. Adding `additionalProperties` is always allowed as long as they satisfy
the requirements of JSON-LD. Dataspaces are encouraged to define normative JSON schemas extending the list of required
and optional properties from the DSP JSON schemas.

## Policies

The Dataspace Protocol `Offer` is a JSON object reusing properties from the Open Digital Rights Language (ODRL).
`Offer`s contain `Constraint`s that signify a boolean operation: They consist of three components
- `leftOperand`: This is (by definition in the JSON-LD context) a IRI that must either be fully qualified in the payload
  or implicitly namespaced by an additional entry in the `@context` object.
- `operator`: This is a reference to the ODRL-IRI of boolean operator used to evaluate a constraint.
- `rightOperand`: Right operands are literals (usually strings) that describe the target value of the Constraint.

`Constraint`s will usually be evaluated against data that is out-of-band from the `Policy` object. For instance, a
policy engine may evaluate a constraint based on a specific claim about the Data Consumer obtained via a Verifiable
Presentation. Which `Constraints` are satisfied by which credentials is Dataspace-specific and requires conventions.
These conventions are codified in **Credential-Constraint-Profiles**. Such a profile should define
1. a set of terms that are valid for `leftOperand`, `operator` and `rightOperand` in the context of the profile.
   Together they constitute the set of supported `Constraints`.
2. validation mechanisms for each `Constraint` and the required side-effects to execute the specified mechanism. This
   may include inspecting an authorization token or querying a remote service.

## Additional Properties on Datasets

To allow a Data Consumer to negotiate only for relevant `odrl:Set`s, a Data Provider should attach meaningful
metadata about the `dcat:Dataset`. They may serve as discriminator for Consumers to expect a certain internal
structure of the Dataset. For example, the navigatable URL-segments of an HTTPS based API may be signified by a
predetermined property.

## Participant Identification

Various DSP messages include an identifier for the participants, like `assigner`, `assignee` and `participantId`.
Dataspaces may have conventions for these properties, for instance as regexes.

## Filtering

The `CatalogRequestMessage` defines a `filter` property that holds a generic json object. Dataspaces are encouraged to 
restrict that object to a well-defined structure allowing  `Data Consumers` to signify the kind of `Datasets` or `Sets`
they are interested in. `Data Providers` shall restrict the returned `Catalog` to content that matches the filter
according to agreed semantics.

## Data Transfer

To cover the data transfer, a Dataspace must agree on a predefined set of transport protocols and define how they are 
signalled in the relevant DSP messages. For each transport protocol, the following properties should be considered when 
further specifying data transfers:

### Catalog Response

As per DSP, the Data Provider may answer to a [`CatalogRequestMessage`](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#catalog-request-message) 
with a [`Catalog - ACK`](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#ack-catalog) message.
Each `Dataset` should include a `Distribution` with a `format` property. Semantically, this property has a definition in
the [DC vocabulary](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/#format). In the context of the
DSP, it's often used to indicate the wire protocol (`HttpData` in this case) as well as the [Transfer Process Type](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/#data-transfer-types) 
(either push or pull). A Dataspace must agree on conventions for this property. Please check the 
`Catalog.dataset.distribution.format` property in the following abbreviated example:

```json
{
  "@context": [
    "https://w3id.org/dspace/2024/1/context.json"
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
It includes the Property `format` that the Consumer replays directly from the appropriate Dataset entry.

```json
{
  "@context": [
    "https://w3id.org/dspace/2024/1/context.json"
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
array. These are unspecified by the DSP but should be further defined for each Transfer Format.

For a pull transfer, this property should be bound to the advertised `format` for the Dataset.

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


