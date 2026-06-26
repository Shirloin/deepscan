# Performance Review Checklist

When reviewing performance, check for every item in this list.
For each issue found, record: file path, line number, description, severity, estimated impact.

## Database Query Efficiency
- [ ] Queries inside loops (N+1 problem)
- [ ] SELECT * returning all columns when only a few are needed
- [ ] Missing WHERE clause on queries that could return full table
- [ ] Missing LIMIT/pagination on queries returning collections
- [ ] JOINs on columns without indexes
- [ ] Multiple sequential queries that could be combined into one
- [ ] Missing database indexes on frequently queried or filtered fields
- [ ] Counting rows with COUNT(*) on large tables without conditions

**Severity guide:** High for N+1 in hot paths, Medium for missing indexes

## Caching
- [ ] Expensive database queries repeated on every request with no cache
- [ ] External API calls made on every request that could be cached
- [ ] Static or rarely-changing data fetched from DB on every request
- [ ] Missing HTTP cache headers on cacheable responses
- [ ] Cache keys not specific enough (cache collisions)

**Severity guide:** Medium for missing cache on expensive ops, High if causing timeouts

## Async and Blocking Operations
- [ ] Blocking synchronous file I/O in async request handlers
- [ ] Synchronous HTTP calls to external services in request path
- [ ] CPU-intensive operations blocking the event loop (Node.js)
- [ ] Long-running tasks executed synchronously instead of queued
- [ ] Missing connection pooling for database or external service connections

**Severity guide:** High if blocking in high-traffic request paths

## Memory
- [ ] Loading entire large datasets into memory when pagination would work
- [ ] Event listeners added but never removed (memory leaks)
- [ ] Large objects kept in memory longer than needed
- [ ] Recursive functions with no depth limit on user-controlled input
- [ ] File reads loading entire file into memory instead of streaming

**Severity guide:** High for obvious memory leaks, Medium for large allocations

## Algorithm Complexity
- [ ] Nested loops over collections that could be replaced with hash maps
- [ ] Linear searches on large arrays that could use indexed structures
- [ ] Sorting inside loops when it could be done once outside
- [ ] Repeated expensive string operations in tight loops
- [ ] Redundant re-computation of values that could be memoized

**Severity guide:** Medium for O(n²) in normal paths, High if user input controls n
