# Overview
This [Go](http://golang.org) package is an extraction of a piece of
instrumentation code I whipped-up for a personal project that a friend of mine
and I are working on.  We were in need for some rudimentary statistics to
observe behaviors of the server's various components, so this was written.

The code here is not a verbatim copy thereof but rather a thoughtful
re-implementation should other folks need to consume and analyze such telemetry.

N.B. --- I have spent a bit of time working through the model in my head and
probably haven't elucidated my ideas as clearly as I need to.  If you examine
src/main.go and src/export/registry.go, you'll find an example of what type of
potential instrumentation use cases this package addresses.  There are probably
numerous Go language idiomatic changes that need to be made, but this task has
been deferred for now.

# Continuous Integration
[![Build Status](https://secure.travis-ci.org/matttproud/golang_instrumentation.png?branch=master)](http://travis-ci.org/matttproud/golang_instrumentation)

# Metrics
A metric is a measurement mechanism.

## Gauge
A Gauge is a metric that exposes merely an instantaneous value or some snapshot
thereof.

## Histogram
A Histogram is a metric that captures events or samples into buckets.  It
exposes its values via percentile estimations.

### Buckets
A Bucket is a generic container that collects samples and their values.  It
prescribes no behavior on its own aside from merely accepting a value,
leaving it up to the concrete implementation to what to do with the injected
values.

#### Accumulating Bucket
An Accumulating Bucket is a bucket that appends the new sample to a timestamped
priority queue such that the eldest values are evicted according to a given
policy.

#### Eviction Policies
Once an Accumulating Bucket reaches capacity, its eviction policy is invoked.
This reaps the oldest N objects subject to certain behavior.

##### Remove Oldest
This merely removes the oldest N items without performing some aggregation
replacement operation on them.

##### Aggregate Oldest
This removes the oldest N items while performing some summary aggregation
operation thereupon, which is then appended to the list in the former values'
place.

#### Tallying Bucket
A Tallying Bucket differs from an Accumulating Bucket in that it never stores
any of the values emitted into it but rather exposes a simplied summary
representation thereof.  For instance, if a values therein is requested,
it may situationally emit a minimum, maximum, an average, or any other
reduction mechanism requested.

# Testing
This package employs [gocheck](http://labix.org/gocheck) for testing.  Please
ensure that all tests pass by running the following from the project root:

    $ go test ./...