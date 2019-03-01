Distributed tracing is a set of tools and practices to monitor the health and reliability of a distributed system. A distributed system consists of multiple components that are deployed and operated separately as micro-services.

The main concept behind distributed tracing is event correlation. Event correlation is a way to associate events originating from one component with events occurring in another. It facilitates the establishment of cause-and-effect relationships between these events. As an example, distributed tracing may be employed to determine the specific sequence of events in which a user's action in a browser component caused a downstream failure in a separate business logic component.

To correlate events between components, a piece of data known as a trace context will need to be exchanged. This data should contain information helpful for the trace, for example mandatory origin event/component identifiers, as well as other event properties. It can also optionally include user-defined values that may additionally aid in event correlation.

The main objective of this draft is to propose a standard for distributed trace contexts as well as to provide alignment on the semantic meaning of the fields within, with the eventual goal that distributed systems implementing this standard can interoperate smoothly.
