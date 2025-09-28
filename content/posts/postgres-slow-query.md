---
date: '2025-09-08T16:26:00+01:00'
draft: false
title: Postgres - I can't replicate a slow query
categories: Database
tags: 
- sql 
- postgres
---

In one of my personal projects I've implemented a `GenServer` to store slow queries. One of 
these queries was returning 17+s seconds to return a result, so I decided to investigate the reason. 

For the context, I'm using a Postgres database and use PgAdmin as UI to test some queries. 
The first step was to run the query on PgAdmin, but got a response immediately. Shouldn't this query take 17+ seconds?

For a long time, I didn't take a further deep dive into this because it wasn't impacting the rest of the application that 
bad, so I just wrote a quick GitHub issue with the details to be checked later. At the time, a quick google search didn't produce any results.

# How to replicate this 

After 3 years, I decided to check this, now with more time to look into it. My first thought was to mimic the application behaviour and use prepared statements. 

The query was something like this:

```sql 
SELECT i0."id", i0."field1" FROM "table1" AS i0 WHERE (((i0."field1" LIKE $1) AND (i0."field2" = $2)) AND ((i0."field3" = 3) AND (i0."field4" >= $3))) AND (i0."id" > $4) ORDER BY i0."id" LIMIT 1
```

I can create a prepare statement by append `PREPARE query1 (text, text, text, int) AS` to the beginning of the query.
Then to execute I can call:

```sql 
EXPLAIN (ANALYZE, BUFFERS) EXECUTE q3('input1%', 'input2', 'input3', 11200000);
```

To delete or modify the query, I need to delete it first, `DEALLOCATE query1;`

Then, by running the query above more than 5 times, the 6th time would take the 17+ seconds instead of the sub second reply.
Now I can try to understand the reason, since the query plan is provided.

The first five times runs in less than 5 ms with the following query plan:
```

Limit  (cost=0.43..7792.39 rows=1 width=76) (actual time=5.224..5.226 rows=1 loops=1)
  Buffers: shared hit=1104 read=81 dirtied=1
  ->  Index Scan using table1_pkey on table1 i0  (cost=0.43..132463.71 rows=17 width=76) (actual time=5.222..5.223 rows=1 loops=1)
        Index Cond: (id > 11200000)
        Filter: (((field1)::text ~~ 'input%'::text) AND ((field2)::text >= 'input2'::text) AND ((field3)::text = 'input3'::text) AND (field4 = 3))
        Rows Removed by Filter: 1577
        Buffers: shared hit=1104 read=81 dirtied=1
Planning Time: 0.440 ms
Execution Time: 5.259 ms
```

The sixth time, it took 21 seconds with the following query plan.

```
Limit  (cost=4839.18..4839.18 rows=1 width=76) (actual time=21123.561..21123.567 rows=1 loops=1)
  Buffers: shared hit=4764 read=131142 dirtied=1612 written=10
  ->  Sort  (cost=4839.18..4839.19 rows=5 width=76) (actual time=21123.558..21123.562 rows=1 loops=1)
        Sort Key: id
        Sort Method: top-N heapsort  Memory: 25kB
        Buffers: shared hit=4764 read=131142 dirtied=1612 written=10
        ->  Bitmap Heap Scan on table1 i0  (cost=4259.79..4839.16 rows=5 width=76) (actual time=3705.783..21123.033 rows=365 loops=1)
              Recheck Cond: (((field1)::text ~~ $1) AND ((field3)::text = $2))
              Rows Removed by Index Recheck: 3645131
              Filter: (((plate_letters)::text >= $3) AND (id > $4) AND (field4 = 3))
              Rows Removed by Filter: 471691
              Heap Blocks: exact=45202 lossy=66193
              Buffers: shared hit=4761 read=131142 dirtied=1612 written=10
              ->  BitmapAnd  (cost=4259.79..4259.79 rows=147 width=0) (actual time=3366.735..3366.737 rows=0 loops=1)
                    Buffers: shared hit=4751 read=19757 written=10
                    ->  Bitmap Index Scan on table1_field1_trgm_idx  (cost=0.00..1798.89 rows=55318 width=0) (actual time=774.775..774.775 rows=480650 loops=1)
                          Index Cond: ((field1)::text ~~ $1)
                          Buffers: shared hit=4750 read=3405 written=7
                    ->  Bitmap Index Scan on table1_field3_idx  (cost=0.00..2460.65 rows=29346 width=0) (actual time=2581.453..2581.454 rows=559635 loops=1)
                          Index Cond: ((field3)::text = $2)
                          Buffers: shared hit=1 read=16352 written=3
Planning Time: 0.229 ms
Execution Time: 21124.522 ms
```

# Solution

Since I can replicate our problem now, I can understand it and find the solution for it.

To achieve a quick result I can for the cache plan to use `force_custom_plan`. 

```sql 
SET plan_cache_mode = force_custom_plan;
```

I didn't use `force_custom_plan`, so I tweak the query so I could achieve similar result without the side effect of being 
too slow by requesting some filters that were too broad.

# Artificial Inteligance (or LLM)

First time I've detected this was August 2022. ChatGPT was first launch in November 2022. 

I've been trying to integrate usage of LLM into my work for some time, but I didn't like the results.
When too specific about what I need it produce wrong results, but I've changed my approach to search 
for more simple stuff. 

After I've solved this, I decided to try to solve it using ChatGPT. Here it [answer](https://chatgpt.com/share/68bf01c6-9560-8001-974b-19a581423ff2).

I've asked to show some solutions how to fix the planning, since it was changing and the first one was to set `force_custom_plan`. 
I ended up using the 4th, rewriting the query.


