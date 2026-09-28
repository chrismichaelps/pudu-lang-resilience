<p align="center">
  <img src="public/pudu-lang-short.png" alt="Pudu" width="120">
</p>

<p align="center">
  <a href="https://www.pudu-lang.org/">Pudu</a> |
  <a href="https://www.pudu-lang.org/docs">Documentation</a> |
  <a href="https://www.pudu-lang.org/packages">Packages</a> |
  <a href="CONTRIBUTING.md">Contributing</a>
</p>

# pudu-lang-resilience

Resilience pipelines for Pudu. A pipeline wraps a callback in layers of policy — retry, circuit
breaker, timeout, fallback, hedging, rate limiting, and chaos injection — and answers one
`Outcome`: the callback's value, or a `Failure` saying why there is none. Nothing is thrown; every
rejection a strategy makes is a value the caller can match on.

```pudu
import PuduLangResilience as Resilience
import PuduLangResilience.Context as Context
import PuduLangResilience.Pipeline as Pipeline
import PuduLangResilience.Retry as Retry
import PuduLangResilience.Timeout as Timeout

fn main() -> Int {
  let pipeline = match Pipeline.build([
      Retry.strategy(Retry.Options{..Retry.defaults(), backoff: Retry.Exponential, useJitter: true}),
      Timeout.strategy(Timeout.after(2000))
    ]) {
    case Ok(built) => built
    case Err(invalid) => panic(Pipeline.explain(&invalid))
  }
  let price = Pipeline.run(&pipeline, fn(context: Context.Context) -> Result[Int, Str] { fetchPrice(&context) })
  if price == Ok(42) { 0 } else { 1 }
}
```

## Installing

```bash
pudu install @chrismichaelps/pudu-lang-resilience
```

It needs [Pudu 0.1.2 or later](https://www.pudu-lang.org/download). Every module the package ships
is `PuduLangResilience` or under it, so it takes no module name from the program that installs it.

## Concepts

| Concept | Module | What it is |
| --- | --- | --- |
| Outcome and failure | `PuduLangResilience` | `Outcome[T, E]` is `Result[T, Failure[E]]`. `Failure` is `Raised(E)`, `TimedOut`, `BrokenCircuit`, `IsolatedCircuit`, `RateLimited`, `Cancelled`, or `Crashed`. |
| Pipeline | `Pipeline` | Strategies composed outermost first. `build`, `buildWith`, `execute`, `run`, `asStrategy`, `describe`, `empty`. |
| Context | `Context` | The operation key, the cancellation token, and typed properties of one execution. |
| Predicate | `Predicate` | Which outcomes a reactive strategy handles: `failures`, `raised`, `results`, `result`, `timeouts`, `brokenCircuits`, `rateLimited`, `anyOf`, `never`. |
| Strategy | `Strategy` | One layer. `Strategy.custom` builds your own; the `Runtime` lends it the pipeline's clock, randomizer, and telemetry. |
| Registry | `Registry` | Named pipelines built once from a builder, shared, and rebuilt with `reload`. |
| Telemetry | `Telemetry`, `Telemetry.Meter`, `Telemetry.Log` | Events with a severity and source, listeners, severity overrides, counters and durations, and one-line logs. |
| Time and chance | `Clock`, `Randomizer` | The system clock or a manual one; the system randomizer, a seeded one, or a fixed one. |

## Strategies

Reactive strategies decide from the outcome; proactive ones decide before or while the callback
runs. Every strategy takes an options record with defaults, and `Pipeline.build` reports every
invalid option at once.

| Strategy | Module | Defaults |
| --- | --- | --- |
| Retry | `Retry` | 3 retries, constant 2 s delay, no jitter; `Constant`, `Linear`, or `Exponential` backoff, `maxDelay`, `delayGenerator`, `onRetry`, `Retry.UNLIMITED` |
| Circuit breaker | `CircuitBreaker` | opens at a failure ratio of 0.1 over at least 100 executions in 30 s, for 5 s; `breakDurationGenerator`, `manualControl`, `stateProvider`, `onOpened`, `onClosed`, `onHalfOpened` |
| Timeout | `Timeout` | 30 s; `generator`, `onTimeout` |
| Fallback | `Fallback` | handles every failure except a cancellation; `action` is required, `withValue` supplies one |
| Hedging | `Hedging` | 1 hedged attempt after 2 s; a delay of 0 starts every attempt at once, `Hedging.WAIT_FOR_OUTCOME` starts the next only after a handled outcome; `actionGenerator`, `delayGenerator`, `onHedging` |
| Rate limiter | `RateLimiter` | a concurrency limiter of 1000 permits and no queue; `concurrency`, `using`, `partitioned`, `onRejected` |

Limiters stand on their own too, under `Limiter`: `Limiter.Concurrency`, `Limiter.TokenBucket`,
`Limiter.FixedWindow`, `Limiter.SlidingWindow`, `Limiter.Partitioned`, and `Limiter.chain`. Each
queues requests up to `queueLimit` permits, oldest or newest first, and answers a `Lease` that is
`Granted`, `Denied` with the wait before a retry when it is known, or `Abandoned` when the token
fired while waiting.

### Chaos

Chaos strategies inject trouble into a share of executions so the rest of the pipeline can be
tested against it. Each takes a `Chaos.Injection`: a rate from 0 to 1 (0.001 by default), an
optional rate generator, and whether it is enabled, optionally per execution.

| Strategy | Module | Injects |
| --- | --- | --- |
| Fault | `Chaos.Fault` | a `Failure` instead of running the callback |
| Outcome | `Chaos.Outcome` | an `Outcome` instead of running the callback |
| Latency | `Chaos.Latency` | a delay before the callback runs (30 s by default) |
| Behavior | `Chaos.Behavior` | an action before the callback; its failure is answered instead |

`Chaos.Weighted.choose` picks among several faults or outcomes by weight.

## Cancellation

Cancellation is cooperative. A callback asks `Context.check(&context)?` between steps and waits
with `Context.pause(&context, millis)?`; both answer `Cancelled` once the token fires. A timeout
answers `TimedOut` when the callback stops because of the timeout's own token; a callback that
ignores its token keeps its value. Hedging cancels the attempts that lost and waits for each of
them before it answers, so no thread outlives the call.

## Testing with a manual clock

Pass `Clock.ofManual(&clock)` and `Randomizer.fixed(0.5d)` or `Randomizer.seeded(42)` in
`Pipeline.Options`. Retry waits, circuit breaks, window limits, and injected latency then move with
`Clock.advance`, and `Clock.sleeps(&clock)` lists every wait a strategy asked for.

## Examples

`examples/` holds runnable programs: `QuickStart`, `CircuitBreaking`, `RateLimiting`, `Hedging`,
`ChaosTesting`, and `Registry`.

```bash
pudu run examples/QuickStart.pudu
```

## Developing

```bash
pudu test test                                        # every suite
pudu fmt --check src test tools examples && pudu lint src test tools examples
pudu run tools/Mutate.pudu --domain --threshold 100   # mutation testing of the pure layer
```

The design lives in the [wiki vault](wiki/00-INDEX.md): one page per source file, the decisions
behind each strategy, and the Pudu grammar rules the code follows.

## License

[Apache License 2.0](LICENSE).
