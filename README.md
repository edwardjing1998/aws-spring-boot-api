Why 409 Conflict is the right choice
HTTP semantics matter here

When a client already exists, it means:

The request syntax is valid

The JSON/body is correct

But the request cannot be completed because of a conflict with current server state

That is exactly what 409 Conflict is defined for.

409 Conflict
The request could not be completed due to a conflict with the current state of the resource.
