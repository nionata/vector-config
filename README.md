# Vector Config

text log over stdin - `vector-stdin.yaml`

```bash
$ echo "hello world" | podman run -i -v $(pwd)/vector-stdin.yaml:/etc/vector/vector.yaml --rm docker.io/timberio/vector:0.42.0-debi
an
2024-11-04T20:12:51.048633Z  INFO vector::app: Log level is enabled. level="info"
2024-11-04T20:12:51.049605Z  INFO vector::app: Loading configs. paths=["/etc/vector/vector.yaml"]
2024-11-04T20:12:51.051040Z  INFO vector::topology::running: Running healthchecks.
2024-11-04T20:12:51.051073Z  INFO vector: Vector has started. debug="false" version="0.42.0" arch="x86_64" revision="3d16e34 2024-10-21 14:10:14.375255220"
2024-11-04T20:12:51.051079Z  INFO vector::app: API is disabled, enable by setting `api.enabled` to `true` and use commands like `vector top`.
2024-11-04T20:12:51.051074Z  INFO vector::topology::builder: Healthcheck passed.
2024-11-04T20:12:51.051090Z  INFO vector::sources::file_descriptors: Capturing stdin.
{"host":"985ccc33308d","message":"hello world","source_type":"stdin","timestamp":"2024-11-04T20:12:51.072433088Z"}
2024-11-04T20:12:51.072582Z  INFO vector_common::shutdown: All sources have finished.
2024-11-04T20:12:51.072597Z  INFO vector_common::shutdown: All sources have finished.
2024-11-04T20:12:51.072606Z  INFO vector::app: All sources have finished.
2024-11-04T20:12:51.072614Z  INFO vector: Vector has stopped.
```

influx db line protocol over http - `vector-influx-http.yaml`

```bash
$ podman run -p 1337:1337 -i -v $(pwd)/vector-influx-http.yaml:/etc/vector/vector.yaml --rm docker.io/timberio/vector:0.42.0-debian
2024-11-04T19:43:01.260562Z  INFO vector::app: Log level is enabled. level="info"
2024-11-04T19:43:01.261747Z  INFO vector::app: Loading configs. paths=["/etc/vector/vector.yaml"]
2024-11-04T19:43:01.262966Z  INFO vector::topology::running: Running healthchecks.
2024-11-04T19:43:01.263001Z  INFO vector: Vector has started. debug="false" version="0.42.0" arch="x86_64" revision="3d16e34 2024-10-21 14:10:14.375255220"
2024-11-04T19:43:01.263008Z  INFO vector::app: API is disabled, enable by setting `api.enabled` to `true` and use commands like `vector top`.
2024-11-04T19:43:01.263026Z  INFO vector::topology::builder: Healthcheck passed.
2024-11-04T19:43:01.263018Z  INFO source{component_kind="source" component_id=in component_type=http_server}: vector::sources::util::http::prelude: Building HTTP server. address=0.0.0.0:1337
2021-08-31T15:37:37Z measurement_field1{tag1="value1",tag2="value2"} = 123
2021-08-31T15:37:37Z measurement_field2{tag1="value1",tag2="value2"} = 1.23
```

```bash
$ curl -d 'measurement,tag1=value1,tag2=value2 field1=123,field2=1.23 1630424257000000000' -X POST 'http://localhost:1337'
```

influx db line protocol over udp socket - `vector-influx-socket.yaml`

```bash
podman run -p 1337:1337/udp -i -v $(pwd)/vector-influx-socket.yaml:/etc/vector/vector.yaml --rm docker.io/timberio/vector:0.42.0-debian
2024-11-04T19:55:32.756927Z  INFO vector::app: Log level is enabled. level="info"
2024-11-04T19:55:32.758096Z  INFO vector::app: Loading configs. paths=["/etc/vector/vector.yaml"]
2024-11-04T19:55:32.759821Z  INFO vector::topology::running: Running healthchecks.
2024-11-04T19:55:32.759855Z  INFO vector: Vector has started. debug="false" version="0.42.0" arch="x86_64" revision="3d16e34 2024-10-21 14:10:14.375255220"
2024-11-04T19:55:32.759860Z  INFO vector::app: API is disabled, enable by setting `api.enabled` to `true` and use commands like `vector top`.
2024-11-04T19:55:32.759868Z  INFO vector::topology::builder: Healthcheck passed.
2024-11-04T19:55:32.759925Z  INFO source{component_kind="source" component_id=in component_type=socket}: vector::sources::socket::udp: Listening. address=0.0.0.0:1337
2021-08-31T15:37:37Z measurement_field1{tag1="value1",tag2="value2"} = 123
2021-08-31T15:37:37Z measurement_field2{tag1="value1",tag2="value2"} = 1.23
```

```bash
$ echo -n "measurement,tag1=value1,tag2=value2 field1=123,field2=1.23 1630424257000000000" | nc -4u -w0 0.0.0.0 1337
```

influx db line protocol over udp socket to file - `vector-influx-socket-file.yaml`

```bash
podman run -p 1337:1337/udp -i -v $(pwd)/vector-influx-socket-file.yaml:/etc/vector/vector.yaml --rm docker.io/timberio/vector:0.42.0-debian
2024-11-04T21:05:14.216066Z  INFO vector::app: Log level is enabled. level="info"
2024-11-04T21:05:14.218669Z  INFO vector::app: Loading configs. paths=["/etc/vector/vector.yaml"]
2024-11-04T21:05:14.220239Z ERROR vector::cli: Configuration error. error=Data type mismatch between in (Metric) and out (Log)
```
