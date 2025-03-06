# Authentication Profiles

## Securing the public DSP endpoints

### HTTPS Binding

HTTPS is the only currently specified transport protocol binding for the DSP. Each of the subprotocols (`Catalog`,
`Contract Negotiation`, `Transfer Process`) have predefined payloads and endpoints.
The `Authorization` header for the request is specified as the appropriate location for the client to present proof (for
example in form of an access token). Its content however is not specified. How a server establishes the proof's
authenticity is unspecified as well. This is an extension point. Dataspaces that require authentication have to agree
on an orthogonal authentication protocol that `ParticipantAgents` agree to present and verify proof.

The Eclipse Decentralized Claims
Protocol's [Verifiable Presentation Protocol](https://eclipse-dataspace-dcp.github.io/decentralized-claims-protocol/#verifiable-presentation-protocol)
can serve as such an authentication
mechanism.

## Participant Identification

Various DSP messages include an identifier for the participants, like `assigner`, `assignee` and `participantId`.
Dataspaces may have conventions for these properties, for instance as regexes.
