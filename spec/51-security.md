# Security

In this section, we present a short analysis on several vulnerabilities that may arise from the implementation of our proposed trace context standard.

Where appropriate, services and platforms relying on `traceparent` and `tracestate` headers should follow computer security best practices by parsing potentially malicious headers defensively. For example, header lengths should be checked and header content should be validated before use. These measures can help prevent common attacks such as buffer overflow or HTML injection attacks.

## Information disclosure

As alluded to in the privacy section, the `traceparent` and `tracestate` headers may contain sensitive information. For example, `traceparent` may allow one call to be correlated to the data sent with another call. [TODO - more description on how this can be used to stage an attack]

As another example of harmful information disclosure, `tracestate` headers containing explicit version information about participating systems can allow an attacker to exploit known vulnerabilities with these systems.

Application owners should either ensure that no proprietary or confidential information is stored in the `tracestate`, and/or it is not leaked to external systems.

## Denial of service

When distributed tracing is enabled on a service with a public API and naively
continues any trace with the `recorded` flag set, a malicious attacker could
overwhelm an application with tracing overhead, forge `trace-id` collisions
that make monitoring data unusable, or run up your tracing bill with your SaaS
tracing vendor. [TODO - please explain this a little more, it is not clear how this is a DoS attack. Also -- can an attacker somehow also abuse this to cause a DDoS attack?]

Tracing vendors and platforms should account for these situations and make sure
that checks and balances are in place to protect denial of monitoring by
malicious or badly authored callers. [TODO - need concrete suggestions on how. This is too generic to be helpful.]

One examples of such protection may be different tracing behavior for
authenticated and unauthenticated requests. Various rate limiters for data
recording can also be implemented. [TODO - explain how these mitigation techniques help. Does
rate limiting mean that we ignore certain traces?]

## Unintended or deliberate modification of trace contexts

An inherent assumption underlying distributed tracing is that every participating component in the distributed system is trustworthy and will behave correctly. This ideal assumption may not hold in every scenario, for example where tracing crosses trust domains, or if there are implementation or Byzantine bugs in the individual components. As a result, forwarded trace contexts may be modified (accidentally or maliciously) such that they are unparseable or misleading. In worse cases, the trace contexts themselves could be deliberately engineered to contain exploits that use the distributed tracing mechanism as a convenient vector for attacking and compromising other systems.

Systems built to accept trace contexts should thus anticipate the possibility of broken or maliciously modified traces, and not naively trust embedded content in `traceparent` or `tracestate`.

[TODO -- consider amending the standard so that each participant signs the trace context whenever it modifies the header. Then no subsequent component can arbitrarily modify preceding header information. But this would require that trace contexts are 'append only'; also more bytes are required to accomodate the signatures].

## Other risks

Application owners need to make sure to test all code paths leading to the sending of `traceparent` and `tracestate` headers. For
example, in single page browser applications it is typical to make cross-origin calls. If one of these code path leads
to the sending of `traceparent` and `tracestate` headers - cross-origin calls restricted via `Access-Control-Allow-Headers` [header](https://www.w3.org/TR/cors/#access-control-allow-headers-response-header), it may fail.
