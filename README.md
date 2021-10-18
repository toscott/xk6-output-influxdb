# xk6-output-influxdb
k6 extension for [InfluxDB v2](https://docs.influxdata.com/influxdb/v2.0), it adds the support for the latest `v2` version and the compatibility API for v1.8+.

#### **Why is this output not directly part of `k6` core?**
The `k6` core already supports the [InfluxDB v1](https://k6.io/docs/results-visualization/influxdb-+-grafana) so the natural feeling would be to do the same for the `v2`. Unfortunately, the `v2` has introduced some breaking changes in the core parts of the API. This would make it difficult to support both versions without taking a bunch of compromises for maintaining the retro-compatibility or introducing breaking changes in the current user experience of the k6's InfluxDB output, with a high probability to create more confusion for k6's users. For this main reason, the `k6` development team has decided to create a new and independent extension for InfluxDB v2.

## How to use

To build a `k6` binary with this extension, first ensure you have the prerequisites:

- [Go toolchain](https://go101.org/article/go-toolchain.html)
- Git
- [xk6](https://github.com/grafana/xk6#install)

1. Build with `xk6`:

```bash
xk6 build --with github.com/grafana/xk6-output-influxdb
```

This will result in a `k6` binary in the current directory.

2. Run with the just built `k6` binary:

```bash
K6_INFLUXDB_ORGANIZATION=<insert-here-org-name> \
K6_INFLUXDB_BUCKET=<insert-here-bucket-name> \
K6_INFLUXDB_TOKEN=<insert-here-valid-token> \
./k6 run -o xk6-influxdb=http://localhost:8086 <script.js>
```

## Configuration

Options for fine-grained control for flushing and connections.


| ENV                           | Default               | Description                                                                                                                                                                                                                                                                                                         |   |
|-------------------------------|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
| K6_INFLUXDB_ORGANIZATION      |                       | The [Organization](https://docs.influxdata.com/influxdb/v2.0/reference/glossary/#organization).                                                                                                                                                                                                                     |   |
| K6_INFLUXDB_BUCKET            |                       | The [Bucket](https://docs.influxdata.com/influxdb/v2.0/reference/glossary/#bucket).                                                                                                                                                                                                                                 |   |
| K6_INFLUXDB_TOKEN             |                       | The [Token](https://docs.influxdata.com/influxdb/v2.0/reference/glossary/#token).                                                                                                                                                                                                                                   |   |
| K6_INFLUXDB_ADDR              | http://localhost:8086 | The address of the instance.                                                                                                                                                                                                                                                                                        |   |
| K6_INFLUXDB_PUSH_INTERVAL     | 1s                    | The flush's frequency of the `k6` metrics.                                                                                                                                                                                                                                                                          |   |
| K6_INFLUXDB_CONCURRENT_WRITES | 4                     | Number of concurrent requests for flushing data. It is useful when a request takes more than the expected time (more than flush interval).                                                                                                                                                                          |   |
| K6_INFLUXDB_TAGS_AS_FIELDS    | vu,iter,url           | A comma-separated string to set `k6` metrics as non-indexable fields (instead of tags). An optional type can be specified using :type as in vu:int will make the field integer. The possible field types are int, bool, float and string, which is the default. Example: vu:int,iter:int,url:string,event_time:int. |   |
| K6_INFLUXDB_INSECURE          | false                 | When `true`, it will skip `https` certificate verification.                                                                                                                                                                                                                                                         |   |
| K6_INFLUXDB_PRECISION         | ns                    | The timestamp [precision](https://docs.influxdata.com/influxdb/v2.0/reference/glossary/#precision).                                                                                                                                                                                                                 |   |

### Compatibility API
The v2 includes a [InfluxDB v1.8+ compatibility API](https://docs.influxdata.com/influxdb/v2.0/reference/api/influxdb-1x) that adds endpoints for communicating with an InfluxDB v1.

>[Client API usage differences summary](https://github.com/influxdata/influxdb-client-go#influxdb-18-api-compatibility):
>
>    1. Use the form username:password for an authentication token. Example: my-user:my-password. Use an empty string ("") if the server doesn't require authentication.
>    2. The organization parameter is not used. Use an empty string ("") where necessary.
>    3. Use the form database/retention-policy where a bucket is required. Skip retention policy if the default retention policy should be used. Examples: telegraf/autogen, telegraf.
