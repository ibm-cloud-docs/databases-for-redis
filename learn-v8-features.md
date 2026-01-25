---

copyright:
  years: 2026
lastupdated: "2026-01-25"

keywords: redis, databases, V8, V7, improvements

subcollection: databases-for-redis

---

{{site.data.keyword.attribute-definition-list}}

# Redis V8 features
{: #v8-features}

Redis V8.2 introduces significant improvements compared to Redis 7.x, by delivering higher performance, enhanced memory efficiency, stronger observability, and improved developer experience. V8.2 offers new capabilities in Streams, bitmaps, vector search, cluster metrics, and preserves the familiar Redis programming model.

The key high-level improvements in V8.2 are as follows:

* Significant performance gains across common commands and workloads.
* Reduced memory usage, especially for JSON and key metadata.
* New functionality in Streams, bit operations, and vector search.
* Expanded metrics and observability for clusters and replication.

## Upgrading from Redis V7.2 to Redis V8.2
{: #v7-v8-upgrading}

For information about how to upgrade from Redis V7.2 to Redis V8.2, see [Upgrading to a new major version](/docs/databases-for-redis?topic=databases-for-redis-upgrading&interface=ui).

## Redis modules
{: #v8-modules}

Redis V8 supports several official modules that extend its functionality for advanced use cases. The key modules are described along with their capabilities.

### RedisBloom
{: #redisbloom}

RedisBloom is an official Redis module that provides probabilistic data structures: Bloom Filter, Cuckoo Filter, Count‑Min Sketch, and Top‑K. These structures trade exactness for extreme space efficiency and speed, making them ideal for when approximate answers with a known error bound are acceptable.

The key capabilities of RedisBloom are as follows:

* Bloom Filter: tests whether an element can be in a set, with configurable false‑positive probability and no false negatives.

* Cuckoo Filter: similar to Bloom but supports deletions more naturally.

* Count‑Min Sketch: estimates the frequency of events in a streaming fashion (for example, how many times a key appears).

* Top‑K: tracks the most frequent items in a data stream.

The typical use cases for RedisBloom are as follows:

* Duplicate and seen‑before checks (URLs, emails, and user IDs).

* Fraud detection and abuse prevention (flag suspicious patterns without storing the full history).

* Recommendation systems and trending lists using Count‑Min Sketch and Top‑K.

* Cache pre‑filters to avoid unnecessary backend lookups.

#### RedisBloom examples
{: #redisbloom_examples}

To create a Bloom filter with 1% error rate and a capacity of 1000:

```sh
> BF.RESERVE bikes:models 0.01 1000

OK
```
{: codeblock}

To add and check a model name:

```sh
> BF.ADD bikes:models "Smoky Mountain Striker"

(integer) 1

> BF.EXISTS bikes:models "Smoky Mountain Striker"

(integer) 1
```
{: codeblock}

For more information, see the [RedisBloom filter documentation](https://redis.io/docs/latest/develop/data-types/probabilistic/bloom-filter/)

****** https://redis.io/docs/latest/develop/data-types/probabilistic/bloom-filter/.redis%E2%80%8B/ doesn't work *******



### RediSearch
{: #redisearch}

RediSearch is a query and indexing engine for Redis that provides full‑text search, secondary indexing, aggregations, and support for vector similarity search. RediSearch indexes Redis data (foe example, hashes and JSON) and enables powerful queries with scoring, filtering, and highlighting.

The key capabilities of RediSearch are as follows:

* Full‑text search with stemming, phonetics, and fuzzy matching.

* Numeric, tag, and geospatial fields for advanced filters.

* Aggregation engine for faceting, analytics, and ranking.

* Integration with vector fields for semantic and ANN search.

The typical use cases for RediSearch are as follows:

* Product catalog search with filters and facets.

* Log and event search with free‑text and structured filters.

* Real-time search over user content (tweets, posts, and documents).

* Combining text and vector search for AI‑powered applications.

#### RediSearch example: create index and add one doc
{: #redisearch_example}

```sh
> FT.CREATE idx ON HASH PREFIX 1 "doc:" SCHEMA title TEXT

OK

> HSET doc:1 title "hello redis"

(integer) 1
```
{: codeblock}

To search by text:

```sh
> FT.SEARCH idx "hello"

1) (integer) 1

2) "doc:1"

3) 1) "title"

   2) "hello redis"

```
{: codeblock}

For more information, see [RediSearch documentation](https://redis.io/docs/latest/develop/ai/search-and-query/).

**** http://redis.io/docs/latest/develop/ai/search-and-query/ doesn't work ****


### RedisJSON
{: #redisjson}

RedisJSON introduces a native JSON data type for Redis, enabling storage, retrieval, and partial updates of JSON documents using JSONPath-like syntax. RedisJSON is optimized for fast access to subdocuments and integrates with other Redis features such as RediSearch.

The key capabilities of RedisJSON are as follows:

* Store structured JSON documents as first‑class values.

* Retrieve or update specific paths without loading the entire document.

* Support for arrays, objects, and nested structures.

* Option to integrate with search and query features for indexing JSON fields.

The typical use cases of RedisJSON are as follows:

* User profiles and sessions with frequently updated nested fields.

* Configuration documents and feature flags.

* Product catalogs or content objects that are shared across services.

* JSON‑based APIs where Redis acts as a high‑speed backing store.

#### RedisJSON example: store a simple JSON document
{: #redisjson_example}

```sh
127.0.0.1:6379> JSON.SET user:1 $ '{"name":"Alice","age":30}'

OK

Get one field:

127.0.0.1:6379> JSON.GET user:1 $.name

"\"Alice\""
```
{: codeblock}

For more information, see the [RedisJSON documentation](https://redis.io/docs/latest/develop/data-types/json/)

****** http://%20https//redis.io/docs/latest/develop/data-types/json/.redis%E2%80%8B doesn't work *****


### RedisTimeSeries
{: #redistimeseries}

RedisTimeSeries is a module for efficient time‑series data ingestion, storage, querying, aggregation, and downsampling. RedisTimeSeries adds a time-series data type and related commands to model metrics and events over time.

The key capabilities of RedisTimeSeries are as follows:

* High-throughput ingestion with automatic timestamping.

* Range queries over time intervals, with aggregations like avg, min, max, and sum.

* Retention policies and compaction rules for long‑term storage.

* Labels for grouping and querying sets of time-series.

The typical use cases of RedisTimeSeries are as follows:

* Infrastructure and application metrics (CPU, memory, latency).

* IoT sensor readings and telemetry.

* Financial tick data and market metrics.

* Business KPIs and operational dashboards.


#### Example: create a time series and add a value
{: #redistimeseries_example}

```sh
> TS.CREATE temp:room1

OK

> TS.ADD temp:room1 * 25.3

(integer) 1736846400000
```
{: codeblock}

Get the latest sample:

```sh
> TS.GET temp:room1

1) (integer) 1736846400000

2) "25.3"
```
{: codeblock}

For more information, see [RedisTimeSeries documentation](https://redis.io/docs/latest/develop/data-types/timeseries/)

***** https://redis.io/docs/latest/develop/data-types/timeseries/.redis%E2%80%8B **** doesn't work

### Redis Vector
{: #redisvector}

Redis Vector provides a vector data type and vector similarity search capabilities, supporting approximate nearest neighbor (ANN) queries over embeddings. This enables AI and ML workloads such as semantic search and recommendations to run directly inside Redis.

The key capabilities of Redis Vector are as follows:

* Storage of high-dimensional vectors (for example, 128–1536 dimensions) with configurable index types (HNSW, flat, and so on.).

* K‑nearest neighbor (KNN) search on vector fields using distance metrics such as cosine, inner product, or L2.

* Hybrid queries that combine vector similarity with filter conditions on structured fields.

* Integration with RediSearch or Query Engine for flexible schemas.

The typical use cases of Redis Vector are as follows:

* Semantic document search for RAG pipelines.

* Personalized recommendations using user or item embeddings.

* Similarity search for images, audio, or code embeddings.

* Fraud and anomaly detection using learned embeddings.

#### Example: minimal vector index and one doc (4-dim example)

```sh
> FT.CREATE vIdx ON HASH PREFIX 1 "vdoc:" \

SCHEMA embedding VECTOR HNSW 6 TYPE FLOAT32 DIM 4 DISTANCE_METRIC COSINE

OK

> HSET vdoc:1 embedding "\x00\x00\x80?\x00\x00\x80?\x00\x00\x80?\x00\x00\x80?"

(integer) 1
```
{: codeblock}

To KNN search for nearest vector:

```sh
> FT.SEARCH vIdx "(*=>[KNN 1 @embedding $v AS score])" \

PARAMS 2 v "\x00\x00\x80?\x00\x00\x80?\x00\x00\x80?\x00\x00\x80?" SORTBY score DIALECT 2

1) (integer) 1

2) "vdoc:1"

3) 1) "embedding"

   2) "\x00\x00\x80?..."

   3) "score"

   4) "0.000000"
```
{: codeblock}

For more information, see [Vector search concepts documentation](https://redis.io/docs/latest/develop/ai/search-and-query/vectors/)


(https://redis.io/docs/latest/develop/ai/search-and-query/vectors/.redis%E2%80%8B) doesn't work


## Changelog and release notes ({{site.data.keyword.databases-for-redis}} 7 to {{site.data.keyword.databases-for-redis}} 8)
{: #v8-changelog}

The functional differences between Redis V7 and Redis V8 are as follows:

* Enhanced Streams commands (XDELEX and XACKDEL) for atomic message lifecycle and cleaner consumer group management.

* Extended bitmap operations through richer BITOP operators for analytics over large bitsets.

* More mature vector search capabilities integrated with the query engine.

* Multiple security and stability fixes that are not present in earlier 7.x releases.

* Distribution and packaging of modules are consolidated in 8.x, simplifying deployment.




## What is {{site.data.keyword.databases-for-redis}}?
{: #what-is-redis}

{{site.data.keyword.databases-for-redis}} is a managed Redis OSS service offered on {{site.data.keyword.Bluemix_notm}}. It is an in-memory _data structure store_ used as a database, cache, message broker, and streaming engine. Unlike traditional databases, which store data on disk, Redis stores data in memory (RAM) enabling low latency. The data is _ephemeral_, which enables {{site.data.keyword.databases-for-redis}} to offer top performance and high throughput. You can also configure Redis to store data persistently on disk by trading off performance with data availability because this is achieved by enabling AOF (Append Only File) sync with RDB snapshots.

RDB snapshots are enabled for backup and high availability, even if persistence is disabled.
{: note}



Understand your data
:   You are expected to know the data types, size, and lifetime of your data. This helps you understand the duration of data available in-memory before it gets deleted or moved.

Estimate memory requirements
:   It is important to calculate your memory requirements. Remember to factor in not only your data, but also the memory required for replication, client connections, max-memory buffers, and Redis metadata.

Understand the read/write loads
:   Identifying your read/write loads helps you prepare for auto-scaling needs, time your application requests, and maintain master-follower sync.
