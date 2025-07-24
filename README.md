Use meaningful names. 
Code to interfaces, but don't overkill. 
Avoid side effect methods. Methods should either return a value or change the state of the invoking object. Avoid changing the state of input parameters as this obscures the code.
Program stateless services.
Follow the single responsibility principle.
Don't repeat yourself.
Code is formatted with the default IntelliJ scheme.
No commented code.
Inputs are validated.
Error scenarios are considered.
Handle nulls properly to prevent NPE.
Files, connections and other resources are released or reused.
Informative logs with appropriate log level.
Avoid expensive queries.
Use non blocking calls to backends whenever possible. Pending discussion
PCI/PII and other sensitive data is encrypted with Voltage before being persisted.
No possibility of SQL injection.
Errors returned to the caller don't reveal system details.
Externalized configurations. No hardcoded values.
No cheating. Jacoco, SpotBugs, and other code quality plugins are there for a reason, do not add exceptions unless it is totally justified.
