# loggregator-api
**WARNING**: This repo is a work in progress and will change.

## v2 Envelope

| Field | Description |
| ----- | ----------- |
| timestamp | UNIX timestamp in nanoseconds. |
| source_uuid | Deterministic id for source of envelope. (*e.g.* `984992f6-3cfb-4417-9321-786ee5233e9c` for an app or `doppler/az3/1` for a doppler)
| tags | key/value tags to include additional identifying information. (*e.g.* `deployment=cf-warden`) |


## v2 Envelope Types

#### Log

A *Log* is used to represent a simple text payload.

It represents whether the log is emitted to STDOUT or STDERR.

#### Counter

A *Counter* is used to represent a metric that only increases in value (*e.g.* `metron.sentEnvelopes`). 

The emitter of a counter must set the `delta` (anything else will be discarded). It also provides the sum of all emitted values.

#### Gauge

A *Gauge* is used to represent a metric that can have arbitary numeric values that increase or decrease. 

It can be used emit a set of relatable metrics (*e.g.* `memory{value=2048, unit=byte}, disk{value=4096, unit=byte}, cpu{value=2, unit=percentage}`)

#### Timer

A *Timer* is used to represent a metric that captures the duration of an event. (*e.g.* `databasePost`)




