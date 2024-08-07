---
sidebar_position: 2
---

# REST API

## 状态码

| 状态码 | 描述                                                 |
| ------ | ---------------------------------------------------- |
| 200    | 请求成功。                                           |
| 204    | 请求成功，异步操作调用成功，不反回请求结果。         |
| 400    | 请求失败，参数错误或缺失。                           |
| 401    | 请求失败，用户名密码错误或用户不存在。               |
| 404    | 请求失败，错误的请求路径。                           |
| 405    | 请求失败，请求的路径不支持对应的请求方式。           |
| 413    | 请求失败，消息体过大，超过限制。                     |
| 422    | 请求失败，操作执行失败。                             |
| 429    | 请求失败，数据库同一时间接受的请求太多，请稍后重试。 |
| 500    | 请求失败，查询超时或外部环境引起的异常。             |
| 503    | 请求失败，服务不可用。                               |

## 接口列表

### `/api/v1/write`

#### 请求方法

- `POST`

#### 请求头

- `Authorizaton: Basic`

    `basic64(user_name + ":" + password)`

#### 请求参数

- `db`：数据库名称（可选，默认 `public`）
- `tanent`：租户名称（可选，默认`cnosdb`）
- `precision`：时间精度（可选，可用值为 `ms`、`us`、`ns`）

#### 请求体

- 行协议：有关行协议的具体内容可以看[这里](https://docs.influxdata.com/influxdb/v1.8/write_protocols/line_protocol_tutorial/)

#### 请求示例

```bash
curl -i -u "username:password" -XPOST "http://localhost:8902/api/v1/write?db=example" -d 't1,foo=a,bar=b v=1'
```

##### 请求成功

```
  HTTP/1.1 200 OK{'\n'}
  content-length: 0{'\n'}
  date: Sat, 08 Oct 2022 06:59:38 GMT{'\n'}
```

##### 请求失败

```
HTTP/1.1 500 Internal Server Error{'\n'}
content-length: 0{'\n'}
date: Sat, 08 Oct 2022 07:03:33 GMT{'\n'}
```

### `/api/v1/sql`

#### 请求方法

- `POST`

#### 请求头

- `Authorizaton: Basic`

  `basic64(user_name + ":" + password)`

#### 请求参数

- `db`：数据库名称（可选，默认 `public`）
- `tenant`：租户名称（可选，默认`cnosdb`）
- `chunked` ：是否流式返回结果数据。默认为`false`。

#### 请求示例

```bash
curl -i -u "username:password" -H "Accept: application/json" -XPOST "http://localhost:8902/api/v1/sql?db=example" -d 'SELECT * from t1'
```

##### 请求成功

```bash
HTTP/1.1 200 OK
content-type: application/json
content-length: 139
date: Sat, 08 Oct 2022 07:17:06 GMT
... ...
```

##### 请求失败

```bash
HTTP/1.1 500 Internal Server Error
content-type: application/json
content-length: 139
date: Sat, 08 Oct 2022 07:17:06 GMT
... ...
```

### `/api/v1/ping`

#### 请求方法

- `GET`
- `HEAD`

#### 请求示例

```bash
curl -G 'http://localhost:8902/api/v1/ping'
```

##### 请求成功

```json
{
"version":"2.x.x",
"status":"healthy"
}
```

##### 请求失败

> 不返回任何结果



### `/api/v1/opentsdb/write`

#### 请求方法

- `POST`

#### 请求头

- `Authorizaton: Basic`

  `basic64(user_name + ":" + password)`

#### 请求参数

- `db`：数据库名称（可选，默认 `public`）
- `tanent`：租户名称（可选，默认`cnosdb`）
- `precision`：时间精度（可选，可用值为 `ms`、`us`、`ns`）

#### 请求体

```text
<metric> <timestamp> <value> <tagk_1>=<tagv_1>[ <tagk_n>=<tagv_n>]
```

#### 请求示例

```bash
curl -i -u "username:password" -XPOST "http://localhost:8902/api/v1/opentsdb/write?db=example" -d 'sys.if.bytes.out 1666165200290401000 1 host=web01 interface=eth0'
```

##### 请求成功

```bash
HTTP/1.1 200 OK
content-length: 0
date: Sat, 08 Oct 2022 06:59:38 GMT
```

##### 请求失败

```
HTTP/1.1 500 Internal Server Error
content-length: 0
date: Sat, 08 Oct 2022 07:03:33 GMT
... ...
```

### `/api/v1/es/_bulk`

#### 请求方法

- `POST`

#### 请求头

- `Authorizaton: Basic`

  `basic64(user_name + ":" + password)`

#### 请求参数

- `db`：数据库名称（可选，默认 `public`）
- `tanent`：租户名称（可选，默认`cnosdb`）
- `table`: 表名称 (必填)
- `log_type`: 日志类型，包括`bulk`、`loki`和`ndjson`（可选，默认`bulk`）
- `time_column`: 指定日志中时间列名称 (可选, 默认`time`。 如果同时没有`time`列和`time_column`会使用当前时间)
- `tag_columns`: 指定日志中多个tag列 (可选，如果没有指定则全部按field列存储)

#### 请求体

- [ES bulk格式](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)

- [Loki格式](https://grafana.com/docs/loki/latest/api/#post-lokiapiv1push)

- [ndjson格式](https://jsonlines.org/)

#### ES bulk请求示例

```bash
curl -i -u "username:password" -XPOST 'http://127.0.0.1:8902/api/v1/es/_bulk?table=t1&time_column=date&tag_columns=node_id,operator_system' -d '{"create":{}}
{"date":"2024-03-27T02:51:11.687Z", "node_id":"1001", "operator_system":"linux", "msg":"test"}
{"index":{}}
{"date":"2024-03-28T02:51:11.688Z", "node_id":"2001", "operator_system":"linux", "msg":"test"}'
```

#### loki请求示例
```bash
curl -i -u "username:password" -XPOST 'http://127.0.0.1:8902/api/v1/es/_bulk?table=t1&log_type=loki' -d '
{"streams": [{ "stream": { "instance": "host123", "job": "app42" }, "values": [ [ "0", "foo fizzbuzz bar" ] ] }]}'
```

#### ndjson请求示例
```bash
curl -i -u "username:password" -XPOST 'http://127.0.0.1:8902/api/v1/es/_bulk?table=t1&log_type=ndjson&time_column=date&tag_columns=node_id,operator_system' -d '{"date":"2024-03-27T02:51:11.687Z", "node_id":"1001", "operator_system":"linux", "msg":"test"}
{"date":"2024-03-28T02:51:11.688Z", "node_id":"2001", "operator_system":"linux", "msg":"test"}'
```

### `/api/v1/traces`

#### 请求方法

- `POST`

#### 请求头

- `Authorizaton: Basic`

  `basic64(user_name + ":" + password)`

- `tanent`：租户名称（可选，默认`cnosdb`）

- `db`：数据库名称（可选，默认 `public`）

- `table`: 表名称 (必填)

#### 使用方法

使用该接口写入opentelemetry trace数据，以下是python示例：
```python
import base64
from opentelemetry.sdk.resources import SERVICE_NAME, Resource

from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

# Service name is required for most backends
resource = Resource(attributes={
    SERVICE_NAME: "test_service"
})
traceProvider = TracerProvider(resource=resource)

# 用户名和密码
username = "root"
password = ""

# 编码用户名和密码
credentials = f"{username}:{password}"
encoded_credentials = base64.b64encode(credentials.encode("utf-8")).decode("utf-8")

# 创建包含身份验证信息的头
headers = {
    "Authorization": f"Basic {encoded_credentials}",
    "tenant": "cnosdb",
    "db": "public",
    "table": "t1",
}

processor = BatchSpanProcessor(OTLPSpanExporter(
    endpoint="http://127.0.0.1:8902/api/v1/traces",
    headers=headers
))
traceProvider.add_span_processor(processor)

trace.set_tracer_provider(traceProvider)

for trace_index in range(10):
    tracer = trace.get_tracer(f"test_trace_{trace_index}")
    with tracer.start_as_current_span(f"trace_{trace_index}_parent_span") as parent_span:        
         with tracer.start_as_current_span("child_span_1") as child_span_1:
               with tracer.start_as_current_span("child_span_2") as child_span_2:
                    tracer.start_as_current_span("child_span_3")

# 关闭TracerProvider以确保所有span都已经被导出
trace.get_tracer_provider().shutdown()
```
