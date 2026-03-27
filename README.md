# Rate Limiter - Sliding Window Implementation

A comprehensive rate limiter implementation in Java with multiple optimization approaches, demonstrating algorithm design, testing, and scalability thinking.

---

## 📌 Problem Statement

Implement a rate limiter that allows at most **N requests** per user in any rolling window of **T seconds**.

**Function signature:**
```java
boolean isAllowed(String userId, int timestamp)
```

**Constraints:**
- Timestamps are **strictly increasing integers** (in seconds)
- Requests can be up to **1 million**
- Number of unique users can be **very large**

**Example:** (N=3, T=10)
```
User A, t=1  → ALLOWED
User A, t=2  → ALLOWED
User A, t=3  → ALLOWED
User A, t=8  → BLOCKED (3 requests in window [1,10])
User A, t=11 → ALLOWED (window [2,11], request at t=1 expired)
```

---

## 📁 Project Files

### Core Implementation
- **Solution.java** - Main sliding window implementation (exact timestamps)
- **SolutionTest.java** - Comprehensive JUnit 5 test suite (21 tests)

### Optimizations
- **OptimizedSolution.java** - Bucket counting (70-90% memory reduction)
- **LRUSolution.java** - LRU cache with bounded memory

### Testing
- **PerformanceComparison.java** - Benchmark tool comparing all solutions

### Documentation
- **README.md** - This file (overview)
- **RATE_LIMITER_SOLUTION.md** - Detailed explanations with examples

---

## 🚀 Quick Start

### Compile
```bash
javac *.java
```

### Run Tests
```bash
# Compile tests
javac -cp .:junit-platform-console-standalone-1.9.3.jar Solution.java SolutionTest.java

# Run all tests (21 tests)
java -jar junit-platform-console-standalone-1.9.3.jar --class-path . --scan-class-path

# Or using IDE: Right-click SolutionTest.java → Run Tests
```

**Test Results:**
```
✔ 21 tests successful
✔ 0 tests failed
Test run finished after 44 ms
```

### Run Performance Comparison
```bash
javac PerformanceComparison.java
java PerformanceComparison
```

**Sample output:**
```
=== Rate Limiter Optimization Comparison ===

【Solution 1】Original       Execution: 14 ms   | Memory: ~18 MB
【Solution 2】Optimized      Execution: 60 ms   | Memory: ~3.8 MB (79% reduction)
【Solution 3】LRU            Execution: 20 ms   | Memory: Fixed (5000 users max)
```

---

## 🎯 Solution Approaches

### 1. Solution.java - Exact Timestamps

**Data Structure:**
```java
Map<String, Deque<Integer>> userRequestLog;
```

**Algorithm:**
1. Calculate window start time
2. Remove expired timestamps
3. Check if count < maxRequests
4. Add timestamp if allowed

**Complexity:**
- **Time:** O(M) per request, M = expired timestamps (typically 1-2)
- **Space:** O(U × N), U = users, N = maxRequests

**When to use:** Small-medium scale (< 10K users), need 100% accuracy

---

### 2. OptimizedSolution.java - Bucket Counting

**Key Idea:** Group timestamps into time buckets instead of storing each one.

**Example:**
```
Without buckets: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]  (10 entries)
With buckets:    bucket[0-4]=5, bucket[5-9]=5     (2 entries)
                 ↑ 80% memory reduction!
```

**Complexity:**
- **Time:** O(B log B), B = buckets (~10)
- **Space:** O(U × B), 70-90% less memory

**When to use:** Medium scale (10K-100K users), need memory efficiency

---

### 3. LRUSolution.java - Bounded Memory

**Key Idea:** Limit max cached users, evict least recently used.

**How it works:**
```
maxUsers = 3

User A → cache: [A]
User B → cache: [A, B]
User C → cache: [A, B, C]  (at capacity)
User D → cache: [B, C, D]  (A evicted - least recent)
```

**Complexity:**
- **Time:** O(M) per request
- **Space:** O(maxUsers × N) - **FIXED upper bound**

**When to use:** Large scale (100K+ users), strict memory constraints

**Trade-off:** Evicted users lose history (acceptable if most users are inactive)

---

## ✅ Key Design Decisions

### Validation Rules
1. **Constructor:**
   - `maxRequests < 0` → exception
   - `windowSize ≤ 0` → exception
   - `maxRequests > windowSize` → exception

2. **Input validation:**
   - `userId == null || isEmpty()` → exception
   - `timestamp < 0` → exception
   - `timestamp ≤ lastTimestamp` → exception (strictly increasing)

### Why Strictly Increasing Timestamps?
- Ensures chronological order
- Prevents concurrent request conflicts
- Simplifies sliding window logic

---

## 🧪 Test Coverage

**SolutionTest.java** includes 21 tests:
- ✅ Happy path (basic rate limiting)
- ✅ Boundary conditions (exactly N requests, window edges)
- ✅ Edge cases (negative params, null userId, zero window)
- ✅ Constructor validation
- ✅ Multi-user scenarios
- ✅ Strictly increasing timestamp enforcement

---

## 🎯 Which Solution to Use?

| Scale | Users | Recommendation |
|-------|-------|----------------|
| **Small** | < 10K | `Solution.java` (simple, fast) |
| **Medium** | 10K - 100K | `OptimizedSolution.java` (memory efficient) |
| **Large** | 100K - 1M | `LRUSolution.java` (bounded memory) |

---

## 📊 Complexity Summary

| Solution | Time | Space | Precision |
|----------|------|-------|-----------|
| **Original** | O(M) | O(U×N) | 100% |
| **Optimized** | O(B log B) | O(U×B) | 95-99% |
| **LRU** | O(M) | O(maxUsers×N) | 90-100%* |

*Depends on cache hit rate

---


## 📖 Additional Resources

- See **RATE_LIMITER_SOLUTION.md** for detailed step-by-step explanations
- Run **PerformanceComparison.java** to benchmark on your machine
# mengxiao-rate-limiter
