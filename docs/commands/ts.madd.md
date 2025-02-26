---
syntax: |
  TS.MADD {key timestamp value}...

---

Append new samples to one or more time series

[Examples](#examples)

## Required arguments

<details open>
<summary><code>key</code></summary> 

is the key name for the time series.
</details>

<details open>
<summary><code>timestamp</code></summary>

is (integer) UNIX sample timestamp in milliseconds or <code>*</code> to set the timestamp according to the server clock.
</details>

<details open>
<summary><code>value</code></summary>

is numeric data value of the sample (double). The double number should follow <a href="https://tools.ietf.org/html/rfc7159">RFC 7159</a> (a JSON standard). The parser rejects overly large values that would not fit in binary64. It does not accept NaN or infinite values.
</details>

<note><b>Notes:</b>
- If `timestamp` is older than the retention period compared to the maximum existing timestamp, the sample is discarded and an error is returned.
- Explicitly adding samples to a compacted time series (using `TS.ADD`, `TS.MADD`, `TS.INCRBY`, or `TS.DECRBY`) may result in inconsistencies between the raw and the compacted data. The compaction process may override such samples.
</note>

## Return value

Either

- @array-reply of @integer-reply representing the timestamp of each added sample or an  @error-reply (e.g., on `DUPLICATE_POLICY` violation)
- @error-reply (e.g., on wrong number of arguments)

## Complexity

If a compaction rule exits on a time series, TS.MADD performance might be reduced.
The complexity of TS.MADD is always `O(N*M)`, where `N` is the amount of series updated and `M` is the amount of compaction rules or `O(N)` with no compaction.

## Examples

<details open>
<summary><b>Add stock prices at different timestamps</b></summary>

Create two stocks and add their prices at three different timestamps.

{{< highlight bash >}}
127.0.0.1:6379> TS.CREATE stock:A LABELS type stock name A
OK
127.0.0.1:6379> TS.CREATE stock:B LABELS type stock name B
OK
127.0.0.1:6379> TS.MADD stock:A 1000 100 stock:A 1010 110 stock:A 1020 120 stock:B 1000 120 stock:B 1010 110 stock:B 1020 100
1) (integer) 1000
2) (integer) 1010
3) (integer) 1020
4) (integer) 1000
5) (integer) 1010
6) (integer) 1020
{{< / highlight >}}
</details>

## See also

`TS.MRANGE` | `TS.RANGE` | `TS.MREVRANGE` | `TS.REVRANGE`

## Related topics

[RedisTimeSeries](/docs/stack/timeseries)
