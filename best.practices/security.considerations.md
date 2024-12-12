# Security related considerations

## Securing the public DSP endpoints

As HTTPS is the only currently specified transport protocol binding for the DSP, this subchapter's content applies to
HTTPS only. Additional bindings will require additional security best-practices. 

Each of the subprotocols (Catalog, `Contract Negotiation`, Transfer Process) have predefined payloads and endpoints. The
`Authorization` header for the request is specified as the appropriate location for the client to present proof. Its
content however is not specified. How a server establishes the proof's authenticity is unspecified as well. This is an
extension point. Dataspaces that require authentication have to agree on an orthogonal authentication protocol that
`ParticipantAgents` agree to present and verify proof.

The Eclipse Decentralized Claims Protocol's [Verifiable Presentation Protocol](https://eclipse-dataspace-dcp.github.io/decentralized-claims-protocol/#verifiable-presentation-protocol) 
can serve as such an authentication mechanism.

## Securing access to `Dataset`s

Granting access to data, i.e., the data transaction, is out of scope of the DSP specification. However, the DSP is a vehicle to exchange 
the necessary information to grant access to such data. Initially, a `Dataset` is advertised in the `Catalog`. Consumers
are encouraged to start the `Contract Negotiation` process. The result of successful the DSP-state-machines' happy path 
is the transfer of a `DataAddress` object. This object declares the means of transferring the business data and may
include (usually short-lived) authorization material. It includes the `endpointProperties` array that will have specific
entries for each [Data Format](interoperability.md#data-formats). 

