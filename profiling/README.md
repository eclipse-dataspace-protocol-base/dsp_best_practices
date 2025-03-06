# Best Practices on Profiling the Dataspace Protocol

The [Dataspace Protocol](https://eclipse-dataspace-protocol-base.github.io/DataspaceProtocol/) defines a set of adaption 
points. They are left initially ambiguous to guarantee agnosticity and extensibility. 
This document describes how to further specify those extension
points for end-to-end interoperability. It suggests three different types of profiles:
- **Policy Profiles**
- **Authentication Profile**
- **Data Transfer Profiles**

## Status of this document

The Best Practices are created and published by the [Eclipse Dataspace Working Group](https://dataspace.eclipse.org/).
They are applicable only to the release candidates for version `2025-1`. Extension mechanisms for earlier versions may
vary.

## General remarks

All DSP message payloads are normatively defined by JSON schemas. Thus, when a profile adds a property or poses further
restrictions on a property, the profile must be formalized as a JSON schema, too. This derivative schema must be valid
JSON-LD and must be a logical subset - meaning that all payloads that validate against the derivative must also validate
against the base schemas from the base spec.