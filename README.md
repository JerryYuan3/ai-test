# ai-test
ai-test
lyra是在开发过程中逐渐孵化出来的。最开始我们用原生的canal、spark程序，再借鉴宜信开源的dbus、wormhole，到最终演化出我们自己的大数据平台。主要包括：元数据管理平台、流计算开发平台、数据监控平台。
元数据管理平台：
日志采集管理：通过固定的规范，固定的流程（Fluentd-->kafka），使用Fluentd采集业务方日志。该模块提供日志的数据字典维护、数据字段与自动ETL联动等功能。
数据采集管理：主要是mysql数据，使用cdc模式，通过flink集成canal-client来获取实时数据，并发送到kafka。
hive数仓：数仓数据字典、数仓数据来源展示
业务数仓：各业务数据库ODS表
Hbase实时数仓：Hbase数仓字典
流计算开发平台：
自动实时ETL功能：大部分的实时etl需求都是一个source对应一个sink，在中间加一个清洗转换操作。我们通过元数据管理已经知道了source/sink的数据字典，转换操作可以使用jsonpath、正则等手段，通过配置的方式即可发布一个实时ETL任务。
flink-sql：提供自定义ddl，flink-sql提交，并能对flink任务进行启动、暂停、停止等管理功能。
数据监控平台：
Fluentd日志收集监控：开发Fluentd插件，每条日志中追加文件名和行号，极大方便数据对账，及时发现数据丢失问题。
Kafka分布式消息系统监控：安装Kafka监控插件，监控日志入prometheus，并使用Grafana对Kafka关键指标进行监控。以Topic和groupId维度监控Topic的Logsize、Offset和Lagsize等指标，当日志量无变化、数据积压等情况时，做到及时发现并预警。
flink任务运行情况监控：使用Flink的Metrics Reporter收集基础Metrics到prometheus，使用Grafana进行展示监控。并以Topic, Partition为维度，监控Kafka Connectors的committedOffsets、currentOffsets等Metrics，以计算出数据积压状况。
