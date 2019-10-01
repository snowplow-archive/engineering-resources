# Dependency graph for Snowplow components

Code for [Webgraphviz](http://www.webgraphviz.com/)

```
digraph G {
  "Snowplow Scala Tracker" -> "Iglu Scala Core"
  "Iglu Scala Client" -> "Iglu Scala Core"
  "Schema DDL" -> "Iglu Scala Core"
  "Scala Analytics SDK" -> "Iglu Scala Core"
  "Spark Enrich" -> "Snowplow Scala Tracker"
  "Stream Enrich" -> "Snowplow Scala Tracker"
  "RDB Loader" -> "Snowplow Scala Tracker"
  "BigQuery Loader" -> "Snowplow Scala Tracker"
  "Scala Common Enrich" -> "Iglu Scala Client"
  "Scala Common Enrich" -> "Bad Rows"
  "Bad Rows" -> "Scala Analytics SDK"
  "Scala Stream Collector" -> "Bad Rows"
  "Snowplow Events Manifest" -> "Iglu Scala Client"
  "RDB Loader" -> "Schema DDL"
  "BigQuery Loader" -> "Schema DDL"
  "igluctl" -> "Schema DDL"
  "Spark Enrich" -> "Scala Common Enrich"
  "Stream Enrich" -> "Scala Common Enrich"
  "RDB Loader" -> "Snowplow Events Manifest"
  "RDB Loader" -> "Scala Analytics SDK"
  "BigQuery Loader" -> "Scala Analytics SDK"
  "Snowflake Loader" -> "Scala Analytics SDK"
}
```

Generate graph, copy content of `<svg>` and paste into [dependencies.html](./dependencies.html).

Follow [this link](https://htmlpreview.github.io/?https://github.com/snowplow-incubator/engineering-resources/blob/dependency-flowchart/img/dependencies.html) to see the graph.