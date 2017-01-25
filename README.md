# loggregator-api
**WARNING**: This repo is a work in progress and will change.

### Table of Contents

* [v2 Envelope](#v2-envelope)
* [v2 Envelope Types](#v2-envelope-types)
  * [Log](#log)
  * [Counter](#counter)
  * [Gauge](#gauge)
  * [Timer](#timer)
* [v2 -> v1 mapping](#v2---v1-mapping)
  * [Envelope](#envelope)
  * [HttpStartStop](#httpstartstop)
  * [LogMessage](#logmessage)
  * [CounterEvent](#counterevent)
  * [ValueMetric](#valuemetric)
  * [ContainerMetric](#containermetric)

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

----

## v2 -> v1 Mapping

The properties in a v1 envelope can be obtained from a v2 envelope using the following mappings:

#### Envelope

|v1 field | maps from v2 field|
|---------|-------------------|
|`Timestamp`|`Envelope.timestamp`|
|`Tags`|`Envelope.tags`|
|`Origin`| `Envelope.Tags['origin'].text`] |
|`Deployment`| `Envelope.Tags['deployment'].text`] |
|`Job`| `Envelope.Tags['job'].text`] |
|`Index`| `Envelope.Tags['index'].text`] |
|`Ip`| `Envelope.Tags['ip'].text`] |


#### HttpStartStop

A *HttpStartStop* envelope is derived from a v2 *Timer* envelope.

|v1 field | maps from v2 field|
|---------|-------------------|
| `startTimestamp` |`Timer.start` |
| `stopTimestamp` | `Timer.stop` |
| `applicationId` | `Envelope.source_uuid`|
| `requestId` | `Envelope.Tags['request_id'].text` |
| `peerType` | `Envelope.Tags['peer_type'].text` |
| `method` | `Envelope.Tags['method'].text` |
| `uri` | `Envelope.Tags['uri'].text`|
| `remoteAddress` | `Envelope.Tags['remote_address'].text` |
| `userAgent` | `Envelope.Tags['user_agent'].text` |
| `statusCode` | `Envelope.Tags['status_code'].integer` |
| `contentLength` | `Envelope.Tags['content_length'].integer` |
| `instanceIndex` | `Envelope.Tags['instance_index'].integer`|
| `instanceId` | `Envelope.Tags['instance_id'].text`|
| `forwarded` | `Envelope.Tags['forwarded'].text`]|

#### LogMessage

A *LogMessage* envelope is derived from a v2 *Log* envelope

|v1 field | maps from v2 field|
|---------|-------------------|
| `message` | `Log.payload`|
| `message_type` | `Log.type`|
| `timestamp` | `Envelope.timestamp`|
| `app_id` | `Envelope.source_uuid`|
| `source_type` | `Envelope.Tags['source_type'].text`|
| `source_instance` | `Envelope.Tags['source_instance'].text` |

#### CounterEvent

A *CounterEvent* envelope is dervied from a v2 *Counter* envelope

|v1 field | maps from v2 field|
|---------|-------------------|
| `name` | `Counter.name`|
| `delta` | *Does not convert*|
| `total` | `Counter.total`|

#### ValueMetric

A *ValueMetric* envelope is dervied from a v2 *Gauge* envelope if and only if there is a single *Gauge* metric.

|v1 field | maps from v2 field|
|---------|-------------------|
| `name` | *`first-key`*|
| `value` | `Gauge.metrics[first-key].value`|
| `unit` | `Gauge.metrics[first-key].unit`|

#### ContainerMetric

A *ContainerMetric* envelope is dervied from a v2 *Gauge* envelope if and only if there are the correct gauge keys.

|v1 field | maps from v2 field|
|---------|-------------------|
| `applicationId` |`Envelope.source_uuid` |
| `instanceIndex` | `Gauge.metrics['instance_index'].value` |
| `cpuPercentage` | `Gauge.metrics['cpu'].value`|
| `memoryBytes` | `Gauge.metrics['memory'].value`|
| `diskBytes` | `Gauge.metrics['disk'].value`|
| `memoryBytesQuota` | `Gauge.metrics['memory_quota'].value`|
| `diskBytesQuota` | `Gauge.metrics['disk_quota'].value`|




