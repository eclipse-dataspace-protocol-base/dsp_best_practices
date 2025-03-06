# Policy Profile

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