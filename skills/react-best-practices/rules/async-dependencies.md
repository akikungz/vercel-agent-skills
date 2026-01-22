---
title: Dependency-Based Parallelization
impact: CRITICAL
impactDescription: 2-10× improvement
tags: async, parallelization, dependencies, better-all
---

## Dependency-Based Parallelization

For operations with partial dependencies, use `better-all` to maximize parallelism. It automatically starts each task at the earliest possible moment. When using native promise combinators prefer `Promise.allSettled()` and handle settled results.

**Incorrect (profile waits for config unnecessarily):**

```typescript
const results = await Promise.allSettled([
  fetchUser(),
  fetchConfig()
])
const [user, config] = results.map(r => r.status === 'fulfilled' ? r.value : undefined)
const profile = await fetchProfile(user.id)
```

**Correct (config and profile run in parallel):**

```typescript
import { all } from 'better-all'

const { user, config, profile } = await all({
  async user() { return fetchUser() },
  async config() { return fetchConfig() },
  async profile() {
    return fetchProfile((await this.$.user).id)
  }
})
```

**Alternative without extra dependencies:**

We can also create all the promises first, and do `Promise.allSettled()` at the end and then map settled results.

```typescript
const userPromise = fetchUser()
const profilePromise = userPromise.then(user => fetchProfile(user.id))

const results = await Promise.allSettled([
  userPromise,
  fetchConfig(),
  profilePromise
])
const [user, config, profile] = results.map(r => r.status === 'fulfilled' ? r.value : undefined)
```

Reference: [https://github.com/shuding/better-all](https://github.com/shuding/better-all)
