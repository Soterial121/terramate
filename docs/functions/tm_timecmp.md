---
title: tm_timecmp - Functions - Configuration Language
description: |-
  The tm_timecmp function adds a duration to a timestamp, returning a new
  timestamp.
---

# `tm_timecmp` Function

`tm_timecmp` compares two timestamps and returns a number that represents the
ordering of the instants those timestamps represent.

```hcl
tm_timecmp(timestamp_a, timestamp_b)
```

| Condition                                          | Return Value |
|----------------------------------------------------|--------------|
| `timestamp_a` is before `timestamp_b`              | `-1`         |
| `timestamp_a` is the same instant as `timestamp_b` | `0`          |
| `timestamp_a` is after `timestamp_b`               | `1`          |

When comparing the timestamps, `timecmp` takes into account the UTC offsets
given in each timestamp. For example, `06:00:00+0200` and `04:00:00Z` are
the same instant after taking into account the `+0200` offset on the first
timestamp.

In the Terraform language, timestamps are conventionally represented as
strings using [RFC 3339](https://tools.ietf.org/html/rfc3339)
"Date and Time format" syntax. `timecmp` requires the its two arguments to
both be strings conforming to this syntax.

## Examples

```
tm_timecmp("2017-11-22T00:00:00Z", "2017-11-22T00:00:00Z")
0
tm_timecmp("2017-11-22T00:00:00Z", "2017-11-22T01:00:00Z")
-1
tm_timecmp("2017-11-22T01:00:00Z", "2017-11-22T00:00:00Z")
1
tm_timecmp("2017-11-22T01:00:00Z", "2017-11-22T00:00:00-01:00")
0
```

`tm_timecmp` can be particularly useful in defining
[custom condition checks](https://developer.hashicorp.com/terraform/language/expressions/custom-conditions) that
involve a specified timestamp being within a particular range. For example,
the following resource postcondition would raise an error if a TLS certificate
(or other expiring object) expires sooner than 30 days from the time of
the "apply" step:

```hcl
  lifecycle {
    postcondition {
      condition     = timecmp(timestamp(), timeadd(self.expiration_timestamp, "-720h")) < 0
      error_message = "Certificate will expire in less than 30 days."
    }
  }
```

## Related Functions

* [`tm_timestamp`](./tm_timestamp.md) returns the current timestamp when it is evaluated
  during the apply step.
* [`tm_timeadd`](./tm_timeadd.md) can perform arithmetic on timestamps by adding or removing a specified duration.
