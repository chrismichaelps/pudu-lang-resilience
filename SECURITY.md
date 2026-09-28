# Security policy

## Reporting a vulnerability

Report a suspected vulnerability privately through GitHub's
[security advisory form](https://github.com/chrismichaelps/pudu-lang-resilience/security/advisories/new),
or by email to <chrisperezsantiago1@gmail.com> with `SECURITY` in the subject.

Please do not open a public issue for a vulnerability. Include the package version, the `pudu`
version, the platform, and the smallest program that shows the problem.

You can expect an acknowledgement within seven days and a decision on whether the report is
accepted within thirty.

## What is in scope

The package runs caller code under policies that bound time, attempts, concurrency, and rate. A
report is in scope when a pipeline does more than its configuration allows:

- A rate limiter or concurrency limiter admitting more executions than its permits and queue.
- A retry, hedging, or chaos strategy running a callback more times than configured.
- A timeout or cancellation that is observed by the callback but not reported to the caller.
- A circuit breaker admitting calls while open or isolated, other than its single half-open probe.
- A thread started by hedging that outlives the call that started it.
- A deadlock or unbounded growth of memory under concurrent use of one pipeline.

## What is not in scope

- A callback that ignores its cancellation token. Cancellation is cooperative, and the documented
  behaviour is that the strategy waits for the callback to return.
- Vulnerabilities in the Pudu compiler or standard library; report those to
  [pudu-lang](https://github.com/chrismichaelps/pudu-lang/security).

## Supported versions

| Version | Supported |
| ------- | --------- |
| 0.1.x   | Yes       |
