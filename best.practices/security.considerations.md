# Security related considerations

## Securing the public DSP endpoints

As HTTPS is the only currently specified transport protocol binding for the DSP, this subchapter's content applies to
HTTPS only. Additional bindings will require additional security best-practices.

Each of the subprotocols (Catalog, `Contract Negotiation`, Transfer Process) have predefined payloads and endpoints. The
`Authorization` header for the request is specified as the appropriate location for the client to present proof (for
example in form of an access token). Its content however is not specified. How a server establishes the proof's 
authenticity is unspecified as well. This is an extension point. Dataspaces that require authentication have to agree 
on an orthogonal authentication protocol that `ParticipantAgents` agree to present and verify proof.

The Eclipse Decentralized Claims Protocol's [Verifiable Presentation Protocol](https://eclipse-dataspace-dcp.github.io/decentralized-claims-protocol/#verifiable-presentation-protocol) can serve as such an authentication 
mechanism.

## Securing access to `Dataset`s

Granting access to data, i.e., the data transaction, is out of scope of the DSP specification. However, the DSP is a
vehicle to exchange the necessary information to grant access to such data. The result of successful the DSP 
interactions is the transfer of a [`DataAddress` object](interoperability.md#dataaddress). This object declares the 
means of transferring the business data and may include (usually short-lived) authorization material in the 
`endpointProperties` array. The set of `EndpointProperty` objects needed to authorize must be specified for each 
[Data Format](interoperability.md#data-formats) and shared between Consumer and Provider.

