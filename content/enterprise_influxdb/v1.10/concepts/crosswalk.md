---
title: Compare InfluxDB to SQL databases
description: Differences between InfluxDB and SQL databases.
menu:
  enterprise_influxdb_1_10:
    name: Compare InfluxDB to SQL databases
    weight: 30
    parent: Concepts
---

InfluxDB is similar to a SQL database, but different in many ways.
InfluxDB is purpose-built for time series data.
Relational databases _can_ handle time series data, but are not optimized for common time series workloads.
InfluxDB is designed to store large volumes of time series data and quickly perform real-time analysis on that data.

### Timing is everything

In InfluxDB, a timestamp identifies a single point in any given data series.
This is like an SQL database table where the primary key is pre-set by the system and is always time.

InfluxDB also recognizes that your [schema](/enterprise_influxdb/v1.10/concepts/glossary/#schema) preferences may change over time.
In InfluxDB you don't have to define schemas up front.
Data points can have one of the fields on a measurement, all of the fields on a measurement, or any number in-between.
You can add new fields to a measurement simply by writing a point for that new field.
If you need an explanation of the terms measurements, tags, and fields check out the next section for an SQL database to InfluxDB terminology crosswalk.

## Terminology

The table below is a (very) simple example of a table  called `foodships` in an SQL database
with the unindexed column `#_foodships` and the indexed columns `park_id`, `planet`, and `time`.

``` sql
+---------+---------+---------------------+--------------+
| park_id | planet  | time                | #_foodships  |
+---------+---------+---------------------+--------------+
|       1 | Earth   | 1429185600000000000 |            0 |
|       1 | Earth   | 1429185601000000000 |            3 |
|       1 | Earth   | 1429185602000000000 |           15 |
|       1 | Earth   | 1429185603000000000 |           15 |
|       2 | Saturn  | 1429185600000000000 |            5 |
|       2 | Saturn  | 1429185601000000000 |            9 |
|       2 | Saturn  | 1429185602000000000 |           10 |
|       2 | Saturn  | 1429185603000000000 |           14 |
|       3 | Jupiter | 1429185600000000000 |           20 |
|       3 | Jupiter | 1429185601000000000 |           21 |
|       3 | Jupiter | 1429185602000000000 |           21 |
|       3 | Jupiter | 1429185603000000000 |           20 |
|       4 | Saturn  | 1429185600000000000 |            5 |
|       4 | Saturn  | 1429185601000000000 |            5 |
|       4 | Saturn  | 1429185602000000000 |            6 |
|       4 | Saturn  | 1429185603000000000 |            5 |
+---------+---------+---------------------+--------------+
```

Those same data look like this in InfluxDB:

```sql
name: foodships
tags: park_id=1, planet=Earth
time			               #_foodships
----			               ------------
2015-04-16T12:00:00Z	 0
2015-04-16T12:00:01Z	 3
2015-04-16T12:00:02Z	 15
2015-04-16T12:00:03Z	 15

name: foodships
tags: park_id=2, planet=Saturn
time			               #_foodships
----			               ------------
2015-04-16T12:00:00Z	 5
2015-04-16T12:00:01Z	 9
2015-04-16T12:00:02Z	 10
2015-04-16T12:00:03Z	 14

name: foodships
tags: park_id=3, planet=Jupiter
time			               #_foodships
----			               ------------
2015-04-16T12:00:00Z	 20
2015-04-16T12:00:01Z	 21
2015-04-16T12:00:02Z	 21
2015-04-16T12:00:03Z	 20

name: foodships
tags: park_id=4, planet=Saturn
time			               #_foodships
----			               ------------
2015-04-16T12:00:00Z	 5
2015-04-16T12:00:01Z	 5
2015-04-16T12:00:02Z	 6
2015-04-16T12:00:03Z	 5
```

Referencing the example above, in general:

* An InfluxDB measurement (`foodships`) is similar to an SQL database table.
* InfluxDB tags ( `park_id` and `planet`) are like indexed columns in an SQL database.
* InfluxDB fields (`#_foodships`) are like unindexed columns in an SQL database.
* InfluxDB points (for example, `2015-04-16T12:00:00Z	5`) are similar to SQL rows.

Building on this comparison of database terminology,
InfluxDB [continuous queries](/enterprise_influxdb/v1.10/concepts/glossary/#continuous-query-cq)
and [retention policies](/enterprise_influxdb/v1.10/concepts/glossary/#retention-policy-rp) are
similar to stored procedures in an SQL database.
They're specified once and then performed regularly and automatically.

Of course, there are some major disparities between SQL databases and InfluxDB.
SQL `JOIN`s aren't available for InfluxDB measurements; your schema design should reflect that difference.
And, as we mentioned above, a measurement is like an SQL table where the primary index is always pre-set to time.
InfluxDB timestamps must be in UNIX epoch (GMT) or formatted as a date-time string valid under RFC3339.

For more detailed descriptions of the InfluxDB terms mentioned in this section see our [Glossary of Terms](/enterprise_influxdb/v1.10/concepts/glossary/).

## Query languages
InfluxDB supports multiple query languages:

- [Flux](#flux)
- [InfluxQL](#influxql)

### Flux

[Flux](/enterprise_influxdb/v1.10/flux/) is a data scripting language designed for querying, analyzing, and acting on time series data.
Beginning with **InfluxDB 1.8.0**, Flux is available for production use along side InfluxQL.

For those familiar with [InfluxQL](#influxql), Flux is intended to address
many of the outstanding feature requests that we've received since introducing InfluxDB 1.0.
For a comparison between Flux and InfluxQL, see [Flux vs InfluxQL](/enterprise_influxdb/v1.10/flux/flux-vs-influxql/).

Flux is the primary language for working with data in [InfluxDB OSS 2.0](/influxdb/v2.0/get-started)
and [InfluxDB Cloud](/influxdb/cloud/get-started/),
a generally available Platform as a Service (PaaS) available across multiple Cloud Service Providers.
Using Flux with InfluxDB 1.8+ lets you get familiar with Flux concepts and syntax
and ease the transition to InfluxDB 2.0.

### InfluxQL

InfluxQL is an SQL-like query language for interacting with InfluxDB.
It has been crafted to feel familiar to those coming from other
SQL or SQL-like environments while also providing features specific
to storing and analyzing time series data.
However **InfluxQL is not SQL** and lacks support for more advanced operations
like `UNION`, `JOIN` and `HAVING` that SQL power-users are accustomed to.
This functionality is available with [Flux](/flux/latest/introduction).

InfluxQL's `SELECT` statement follows the form of an SQL `SELECT` statement:

```sql
SELECT <stuff> FROM <measurement_name> WHERE <some_conditions>
```

where `WHERE` is optional.

To get the InfluxDB output in the section above, you'd enter:

```sql
SELECT * FROM "foodships"
```

If you only wanted to see data for the planet `Saturn`, you'd enter:

```sql
SELECT * FROM "foodships" WHERE "planet" = 'Saturn'
```

If you wanted to see data for the planet `Saturn` after 12:00:01 UTC on April 16, 2015, you'd enter:

```sql
SELECT * FROM "foodships" WHERE "planet" = 'Saturn' AND time > '2015-04-16 12:00:01'
```

As shown in the example above, InfluxQL allows you to specify the time range of your query in the `WHERE` clause.
You can use date-time strings wrapped in single quotes that have the
format `YYYY-MM-DD HH:MM:SS.mmm`
(`mmm` is milliseconds and is optional, and you can also specify microseconds or nanoseconds).
You can also use relative time with `now()` which refers to the server's current timestamp:

```sql
SELECT * FROM "foodships" WHERE time > now() - 1h
```

That query outputs the data in the `foodships` measure where the timestamp is newer than the server's current time minus one hour.
The options for specifying time durations with `now()` are:

|Letter|Meaning|
|:---:|:---:|
| ns | nanoseconds |
|u or µ|microseconds|
| ms | milliseconds |
|s | seconds   		|
| m        | minutes   		|
| h        | hours   		|
| d        | days   		|
| w        | weeks   		|

InfluxQL also supports regular expressions, arithmetic in expressions, `SHOW` statements, and `GROUP BY` statements.
See our [data exploration](/enterprise_influxdb/v1.10/query_language/explore-data/) page for an in-depth discussion of those topics.
InfluxQL functions include `COUNT`, `MIN`, `MAX`, `MEDIAN`, `DERIVATIVE` and more.
For a full list check out the [functions](/enterprise_influxdb/v1.10/query_language/functions/) page.

Now that you have the general idea, check out our [Getting Started Guide](/enterprise_influxdb/v1.10/introduction/getting-started/).

## InfluxDB is not CRUD

InfluxDB is a database that has been optimized for time series data.
This data commonly comes from sources like distributed sensor groups, click data from large websites, or lists of financial transactions.

One thing this data has in common is that it is more useful in the aggregate.
One reading saying that your computer’s CPU is at 12% utilization at 12:38:35 UTC on a Tuesday is hard to draw conclusions from.
It becomes more useful when combined with the rest of the series and visualized.
This is where trends over time begin to show, and actionable insight can be drawn from the data.
In addition, time series data is generally written once and rarely updated.

The result is that InfluxDB is not a full CRUD database but more like a CR-ud, prioritizing the performance of creating and reading data over update and destroy, and [preventing some update and destroy behaviors](/enterprise_influxdb/v1.10/concepts/insights_tradeoffs/) to make create and read more performant:

* To update a point, insert one with [the same measurement, tag set, and timestamp](/enterprise_influxdb/v1.10/troubleshooting/frequently-asked-questions/#how-does-influxdb-handle-duplicate-points).
* You can [drop or delete a series](/enterprise_influxdb/v1.10/query_language/manage-database/#drop-series-from-the-index-with-drop-series), but not individual points based on field values. As a workaround, you can search for the field value, retrieve the time, then [DELETE based on the `time` field](/enterprise_influxdb/v1.10/query_language/manage-database/#delete-series-with-delete).
* You can't update or rename tags yet - see GitHub issue [#4157](https://github.com/influxdata/influxdb/issues/4157) for more information. To modify the tag of a series of points, find the points with the offending tag value, change the value to the desired one, write the points back, then drop the series with the old tag value.
* You can't delete tags by tag key (as opposed to value) - see GitHub issue [#8604](https://github.com/influxdata/influxdb/issues/8604).
