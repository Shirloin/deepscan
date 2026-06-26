# Performance Review Checklist

When reviewing performance, check every section in this file against the codebase.
Applies to all tech stacks: React, Vue, Angular, Next.js, Node.js, Python, Java, Go, PHP, and others.

For each violation found, record:
- **File** and **line number**
- **What the violation is**
- **Expected behavior** including a solution suggestion

---

## 1. Database Query Efficiency

### 1.1 N+1 Query Problem
- [ ] A query executed inside a loop where one outer query fetches a list, then N queries fetch related data
- [ ] ORM lazy loading in a loop (`for user in users: user.orders.all()`)
- [ ] Fetching parent records then querying children in a loop
- [ ] GraphQL resolvers fetching related data per item without a DataLoader

**Severity:** High — especially in hot paths or on large datasets
**Expected behavior:** Use eager loading / joins to fetch related data in one query. In SQL ORMs: use `include`, `joins`, `select_related`, `prefetch_related`. In GraphQL: implement DataLoader for batching. Aim for a fixed number of queries regardless of result set size.

### 1.2 Unbounded Queries
- [ ] `SELECT *` or `findAll()` with no `LIMIT` clause
- [ ] List endpoints that return all records when the table could have millions of rows
- [ ] No pagination applied before querying the database
- [ ] `count()` queries on large tables without indexed conditions

**Severity:** High
**Expected behavior:** Every query that returns a collection must have a LIMIT. Apply pagination at the database level — never fetch all and slice in memory. Default page size should be 20–100 rows. Use `COUNT()` with WHERE clauses on indexed columns only.

### 1.3 Selecting More Data Than Needed
- [ ] `SELECT *` when only 2–3 fields are needed
- [ ] Fetching entire ORM models when only specific fields are used
- [ ] Loading entire related object graphs when only one field from a relation is needed

**Expected behavior:** Select only the columns needed. Use `.only()`, `.values()`, projection in ORM. For APIs, use a DTO/serializer that selects only what the response needs. Reduces data transfer, memory usage, and query time.

### 1.4 Missing Database Indexes
- [ ] Queries filtering on columns that are not indexed
- [ ] Foreign key columns without indexes
- [ ] Columns used in `ORDER BY`, `GROUP BY`, or `JOIN ON` without indexes
- [ ] Composite queries filtering on multiple columns with no composite index
- [ ] Full table scans where an index scan should be used

**Severity:** High on large tables
**Expected behavior:** Add indexes on all columns used in WHERE clauses, JOIN conditions, and ORDER BY. For multi-column filters, add a composite index matching the query pattern. Use `EXPLAIN` / `EXPLAIN ANALYZE` to verify index usage.

### 1.5 Inefficient Query Patterns
- [ ] Multiple sequential queries that could be one query with a JOIN
- [ ] Fetching a record just to check if it exists, then fetching it again to use it
- [ ] Using `LIKE '%search%'` on large tables with no full-text index
- [ ] Subqueries in loops that could be a single query with IN or JOIN
- [ ] Ordering by non-indexed columns on large result sets

**Expected behavior:** Combine related queries. Use `EXISTS` instead of fetching to check existence. Use full-text search indexes for text search. Use `WHERE id IN (...)` for batch lookups instead of multiple individual queries.

---

## 2. Caching

### 2.1 Missing Caching on Expensive Operations
- [ ] Database queries that return the same result for every request with no cache
- [ ] External API calls made on every request when the data changes infrequently
- [ ] Expensive computations (aggregations, reports) recalculated on every request
- [ ] Static or rarely-changing data (config, feature flags, lookup tables) fetched from DB every time

**Expected behavior:** Cache results of expensive, frequently-called, and infrequently-changing operations. Use Redis, Memcached, or in-memory cache. Set appropriate TTL based on how often the data changes. Use cache-aside pattern: check cache first, hit DB on miss, populate cache.

### 2.2 Missing HTTP Caching
- [ ] API responses for static or slowly-changing data not setting cache headers
- [ ] No `Cache-Control`, `ETag`, or `Last-Modified` headers on cacheable responses
- [ ] CDN not used for static assets in frontend applications
- [ ] Frontend API calls repeating on every render without client-side caching

**Expected behavior:** Set `Cache-Control` headers for cacheable responses. Use `ETag`/`Last-Modified` for conditional requests. Put static assets behind a CDN. Use a client-side cache (React Query, SWR, Apollo Client) for API responses in frontend apps.

### 2.3 Cache Invalidation Problems
- [ ] Cache never invalidated — stale data served indefinitely
- [ ] Cache invalidated too aggressively — cache never actually helps
- [ ] No cache invalidation when underlying data changes
- [ ] Cache key not specific enough — different users seeing each other's cached data

**Expected behavior:** Define clear TTL for each cache type. Implement explicit cache invalidation on data mutation. Include all relevant parameters in cache keys (user ID, filters, version). Use write-through or event-driven cache invalidation for critical data.

---

## 3. Memory Management

### 3.1 Memory Leaks
- [ ] Event listeners added in component lifecycle but never removed on unmount
- [ ] Timers (`setInterval`, `setTimeout`) started but never cleared
- [ ] WebSocket or SSE connections not closed on component unmount
- [ ] Closures capturing large objects keeping them in memory longer than needed
- [ ] Growing arrays or maps that are never cleared (accumulating log entries, listeners, etc.)
- [ ] Observable subscriptions (RxJS) not unsubscribed on destroy

**Severity:** High — causes degradation over time in long-running applications
**Expected behavior:** Always clean up in component unmount / destructor / `useEffect` return function. Use `AbortController` to cancel fetch requests. Use WeakMap/WeakRef for objects that should be garbage-collected. Profile memory usage periodically in staging.

### 3.2 Loading Too Much Data Into Memory
- [ ] Reading entire large files into memory with `readFile` instead of streaming
- [ ] Fetching and storing thousands of records in memory for processing
- [ ] Storing large payloads in session or application-level variables
- [ ] Processing CSV/JSON files line-by-line in memory when stream processing is possible

**Expected behavior:** Use streams for file processing. Process large datasets in batches (e.g., 500 records at a time). Never hold large datasets in memory that could be processed sequentially. Use generators or async iterators for lazy evaluation.

### 3.3 Redundant Data in Memory
- [ ] Entire database record stored in session when only an ID is needed
- [ ] Full response bodies cached when only specific fields are needed
- [ ] Duplicate copies of the same data in different parts of application state

**Expected behavior:** Store only what's needed in memory. Reference by ID and look up when needed rather than keeping full objects. Normalize state to avoid duplication.

---

## 4. Async and Blocking Operations

### 4.1 Blocking the Event Loop (Node.js / Single-Threaded Runtimes)
- [ ] Synchronous file I/O (`fs.readFileSync`, `fs.writeFileSync`) in request handlers
- [ ] CPU-intensive operations (crypto, image processing, large JSON parsing) on the main thread
- [ ] Synchronous HTTP calls to external services
- [ ] Large `JSON.parse()` or `JSON.stringify()` on the main thread for very large payloads
- [ ] Long-running loops with no yielding

**Severity:** High in Node.js — blocks all concurrent requests
**Expected behavior:** Use async I/O exclusively in request handlers. Offload CPU-intensive work to worker threads or a separate service. Use streaming for large JSON payloads. For Node.js: `fs.promises`, `util.promisify`, never `*Sync` variants in request handling.

### 4.2 Missing Parallelization
- [ ] Sequential `await` for independent async operations that could run concurrently
- [ ] Multiple independent API calls made one after another instead of in parallel
- [ ] Independent database queries run sequentially

**Expected behavior:** Use `Promise.all()` for independent parallel operations. `await Promise.all([fetchA(), fetchB(), fetchC()])` instead of three sequential awaits. Only use sequential awaits when the result of one is needed as input to the next.

### 4.3 Missing Connection Pooling
- [ ] New database connection created on every request
- [ ] New HTTP client instance created on every request
- [ ] No connection pool configured for database client
- [ ] Connection pool size not tuned for the expected concurrency

**Expected behavior:** Configure a connection pool for all database clients. Reuse HTTP client instances (axios instance, http.Agent). Set pool size based on expected concurrency and database server limits. Monitor pool utilization.

### 4.4 Long-Running Tasks in Request Path
- [ ] Sending emails synchronously during a request
- [ ] Generating large reports synchronously in a request
- [ ] Processing image or video uploads synchronously
- [ ] Bulk operations performed in a single synchronous request

**Expected behavior:** Move long-running tasks to a background job queue (Bull, Celery, Sidekiq, SQS). Return a 202 Accepted immediately and process asynchronously. Notify the client via webhook, polling endpoint, or WebSocket when complete.

---

## 5. Algorithm and Computational Complexity

### 5.1 Unnecessary O(n²) or Worse
- [ ] Nested loops over the same collection (finding duplicates, matching items)
- [ ] Array `includes()` or `find()` inside a loop over another array
- [ ] Building result sets by repeatedly searching through an array instead of a hash map
- [ ] Sorting inside a loop when sorting could be done once outside

**Expected behavior:** Replace nested array searches with hash map / Set lookups (O(1)). Build lookup maps before loops: `const userMap = new Map(users.map(u => [u.id, u]))`. Sort once outside any loop. O(n log n) is usually acceptable; O(n²) rarely is.

### 5.2 Repeated Expensive Computation
- [ ] Same derived value recalculated on every render or every call
- [ ] Same regex compiled on every function call instead of once at module level
- [ ] Same string parsing or object transformation repeated in a loop
- [ ] Memoizable pure functions called repeatedly with the same arguments

**Expected behavior:** Memoize pure functions. Compile regexes once at module level. Use `useMemo` in React for expensive derived values. Cache computed results when the inputs don't change.

### 5.3 Inefficient String Operations
- [ ] String concatenation in a loop (creates new string object each iteration)
- [ ] Multiple regex passes over the same string when one pass would work
- [ ] Large template literal construction inside tight loops

**Expected behavior:** Use array `join()` for building large strings in loops. Combine regex patterns where possible. Move string construction outside of loops.

### 5.4 Inefficient Collection Operations
- [ ] Calling `array.length` in every loop iteration when it doesn't change (minor, but signals)
- [ ] Using `Array.find()` when a Set lookup would be faster for membership tests
- [ ] Repeatedly calling `Object.keys()` or `Object.entries()` on the same unchanged object
- [ ] Filtering then mapping (two passes) when one `reduce` would work

**Expected behavior:** Use the right data structure for the access pattern. Sets for membership tests. Maps for keyed lookups. Combine operations where possible. Cache collection length if iterating a non-changing collection.

---

## 6. Frontend Performance (React, Vue, Angular, Next.js)

### 6.1 Unnecessary Re-renders
- [ ] Parent component re-renders causing all children to re-render unnecessarily
- [ ] Inline object or function creation in JSX props (new reference on every render)
- [ ] Missing `React.memo`, `useMemo`, or `useCallback` on expensive child components
- [ ] State updates that don't change the visible output still triggering re-renders
- [ ] Context value changing on every render causing all consumers to re-render

**Expected behavior:** Use `React.memo` for pure child components. Use `useCallback` for function props. Use `useMemo` for expensive computed values. Avoid creating objects/arrays inline in JSX. Split Context into smaller pieces so changes don't invalidate unrelated consumers.

### 6.2 Bundle Size
- [ ] Importing entire libraries when only one function is needed (`import _ from 'lodash'`)
- [ ] No code splitting — entire app in one bundle
- [ ] Large dependencies included that have lighter alternatives
- [ ] Images not optimized or compressed
- [ ] No lazy loading for routes or heavy components

**Expected behavior:** Use tree-shakeable imports (`import { debounce } from 'lodash-es'`). Implement route-level code splitting with `React.lazy` or Next.js dynamic imports. Analyze bundle with `webpack-bundle-analyzer` or `vite-bundle-visualizer`. Lazy load below-the-fold images.

### 6.3 Render-Blocking Operations
- [ ] Data fetching started in `useEffect` instead of during rendering (waterfall loading)
- [ ] No loading states — UI blocks until all data is ready
- [ ] Large synchronous operations in the render path
- [ ] Third-party scripts blocking the main thread

**Expected behavior:** Use React Suspense with data fetching libraries (React Query, SWR) for parallel data fetching. Implement skeleton loading states. Defer non-critical third-party scripts. Use `startTransition` for non-urgent state updates.

### 6.4 Missing Virtualization for Large Lists
- [ ] Rendering 100+ items in a list without virtualization
- [ ] Long tables with all rows in the DOM simultaneously
- [ ] Infinite scroll loading all items into DOM

**Expected behavior:** Use virtualization (react-window, react-virtual, TanStack Virtual) for lists with 50+ items. Only render items visible in the viewport. Properly sized containers for the virtualization library to calculate item positions.

---

## 7. API and Network Performance

### 7.1 Over-fetching and Under-fetching
- [ ] API returning 50 fields when the consumer needs 3
- [ ] Multiple API calls needed to get all data for one page (waterfall requests)
- [ ] No field selection/projection available on data-heavy endpoints
- [ ] Frontend making redundant API calls for the same data

**Expected behavior:** Design API responses to match consumer needs. Consider GraphQL or field projection for flexible selection. Combine related data in one response where appropriate. Use a client-side cache to avoid redundant requests.

### 7.2 No Request Debouncing or Throttling (Frontend)
- [ ] Search input triggering an API call on every keystroke
- [ ] Scroll event handlers making API calls without throttling
- [ ] Form change handlers triggering saves on every change
- [ ] Resize or mousemove handlers running expensive operations at full frequency

**Expected behavior:** Debounce search inputs (300–500ms delay). Throttle scroll and resize handlers. Use `requestAnimationFrame` for visual updates. Debounce auto-save operations.

### 7.3 Missing Compression
- [ ] API responses not gzip or Brotli compressed
- [ ] Large JSON payloads sent without compression
- [ ] Static assets served without compression

**Expected behavior:** Enable gzip/Brotli compression at the server or reverse proxy level. Compress all text-based responses over 1KB. Verify with `Content-Encoding: gzip` in response headers.

---

## 8. Cross-File Performance Patterns (requires full repository read)

### 8.1 Duplicated Expensive Operations Across Files
- [ ] Same expensive database query written in multiple service files instead of shared
- [ ] Same external API call made in multiple places without a shared cached service
- [ ] Same data transformation repeated in 5 different files instead of a shared utility
- [ ] Each file instantiating its own HTTP client or DB connection instead of sharing one

**Expected behavior:** Any operation that is expensive (DB query, external API call, complex computation) and needed in more than one place must be abstracted into a shared service with caching. One shared instance of HTTP clients and DB connections — never one per file.

### 8.2 No Consistent Caching Strategy
- [ ] Some services cache their results, others don't — with no clear rule about what should be cached
- [ ] Some caches use Redis, others use in-memory objects, others use files — with no standard
- [ ] Cache TTLs set arbitrarily in each file with no central policy
- [ ] Some responses have cache headers, most don't — no consistent HTTP caching policy

**Expected behavior:** Define a caching strategy for the project: what gets cached, where (Redis vs in-memory), and for how long (TTL policy by data type). Apply consistently. Centralize cache key generation to avoid collisions across files.