---
title: Promise.allSettled() for Independent Operations
impact: CRITICAL
impactDescription: 2-10× improvement
tags: async, parallelization, promises, waterfalls
---

## Promise.allSettled() for Independent Operations

When async operations have no interdependencies, execute them concurrently using `Promise.allSettled()` and handle settled results.

**Incorrect (sequential execution, 3 round trips):**

```typescript
const user = await fetchUser()
const posts = await fetchPosts()
const comments = await fetchComments()
```

**Correct (parallel execution, 1 round trip):**

```typescript
const results = await Promise.allSettled([
  fetchUser(),
  fetchPosts(),
  fetchComments()
])
const [user, posts, comments] = results.map(r => r.status === 'fulfilled' ? r.value : undefined)
```
