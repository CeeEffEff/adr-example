# Tutorial: Creating Your First ADR

This tutorial walks through creating a realistic Architecture Decision Record (ADR) from start to finish, demonstrating how to think about and document an architectural decision using the MADR template.

## Scenario: Choosing a Caching Strategy

Imagine you're building a web application that's experiencing performance issues due to repeated database queries. You need to decide on a caching strategy. This is a perfect candidate for an ADR because:

- It's **significant** - Caching affects performance, complexity, and operational costs
- It's **high-impact** - Multiple parts of the system will depend on this decision
- It has **multiple valid options** - Redis, Memcached, application-level caching, etc.

## Step 1: Create the ADR File

Run the Log4brains command to create a new ADR:

```bash
npx log4brains adr new "Use Redis for application caching"
```

**Interactive prompts:**
- Status: Choose `proposed` (decisions start as proposals before acceptance)
- Deciders: Enter your name or team name (e.g., "Backend Team")
- Tags: Add relevant tags like `caching, performance, redis, infrastructure`

This creates a file: `docs/adr/YYYYMMDD-use-redis-for-application-caching.md`

## Step 2: Fill Out the Context and Problem Statement

**Purpose:** Explain *why* you need to make this decision. Focus on the problem, not the solution.

**Good example:**
```markdown
## Context and Problem Statement

Our web application serves 10,000+ daily active users, with peak traffic during business hours (9am-5pm EST). Database profiling shows that 60% of queries are repetitive reads for user profiles, product catalogs, and configuration data. Average page load time is 2.3 seconds, with 800ms spent on database queries.

We need a caching strategy to reduce database load and improve response times. The solution must handle cache invalidation for data that updates frequently (user sessions) while supporting longer TTLs for relatively static data (product catalogs).

Should we introduce a dedicated caching layer, and if so, which technology best fits our requirements?
```

**Why this works:**
- Quantifies the problem (60% repetitive queries, 2.3s load time)
- Describes the context (10K users, traffic patterns)
- Identifies specific requirements (cache invalidation, different TTLs)
- Ends with a clear decision question

**Poor example (avoid):**
```markdown
## Context and Problem Statement

The app is slow. We should use caching.
```

This lacks context, doesn't quantify the problem, and jumps to conclusions.

## Step 3: Identify Decision Drivers

**Purpose:** List the factors that influence your decision - requirements, constraints, and priorities.

**Example:**
```markdown
## Decision Drivers

- **Performance**: Target <1 second page load times (50% improvement)
- **Scalability**: Must handle 5x growth without major architectural changes
- **Operational complexity**: Small DevOps team; prefer managed services or simple self-hosted solutions
- **Cost**: Budget limit of $200/month for caching infrastructure
- **Data consistency**: Tolerate eventual consistency for most data; strong consistency for user sessions
- **Developer experience**: Team has more Python experience than DevOps; need good Python client libraries
- **High availability**: 99.9% uptime requirement for production
```

**Tips:**
- Use specific numbers when possible (target metrics, budget constraints)
- Include both technical and business drivers
- Note team constraints (expertise, time, resources)
- Distinguish between "must-haves" and "nice-to-haves"

## Step 4: List Considered Options

**Purpose:** Show that you evaluated alternatives, not just picked your favorite technology.

**Example:**
```markdown
## Considered Options

- **Option 1**: Redis (in-memory data store with persistence)
- **Option 2**: Memcached (pure in-memory cache)
- **Option 3**: Application-level caching (Python `cachetools` library)
- **Option 4**: Django's built-in cache framework with database backend
- **Option 5**: Managed cache service (AWS ElastiCache, Google Cloud Memorystore)
```

**Tips:**
- Include 3-5 realistic options (not 20)
- Brief descriptions help readers unfamiliar with the technologies
- Include the "do nothing" or "simplest" option if relevant

## Step 5: Document the Decision Outcome

**Purpose:** State what you chose and *why*. The justification is critical.

**Example:**
```markdown
## Decision Outcome

Chosen option: "**Redis (self-hosted on AWS EC2)**", because it offers the best balance of features, cost, and operational complexity for our current scale.

**Justification:**
- Redis's data structures (sets, sorted sets, hashes) support our use cases beyond simple key-value storage
- Persistence options (RDB snapshots) provide resilience against cache cold-starts after restarts
- Excellent Python client libraries (redis-py) with strong community support
- Self-hosted deployment stays within budget ($200/month) while providing production-grade performance
- Our team has prior Redis experience from previous projects, reducing learning curve
- Active-active replication supports our high availability requirement

### Positive Consequences

- **Performance**: Expect 70-80% reduction in database queries based on Redis benchmarks
- **Flexibility**: Rich data structures enable future features (e.g., real-time leaderboards using sorted sets)
- **Developer productivity**: Well-documented Python client accelerates development
- **Cost control**: Self-hosted deployment at ~$150/month fits budget with room for growth
- **Monitoring**: Redis provides built-in metrics via INFO command for observability

### Negative Consequences

- **Operational burden**: Requires monitoring, backup, and maintenance of Redis instances
- **Cache warming**: Cold cache after deployments may cause temporary performance degradation
- **Data consistency complexity**: Need to implement cache invalidation strategies (TTLs, event-driven invalidation)
- **Single point of failure**: Without Redis, application performance degrades significantly (requires careful failover planning)
- **Memory constraints**: In-memory storage limits cache size; may need tiered caching in the future
```

**Tips:**
- Be explicit about trade-offs (positive AND negative consequences)
- Tie consequences back to decision drivers when possible
- Quantify impacts when you can ("70-80% reduction")
- Acknowledge risks and mitigation needs

## Step 6: Detail Pros and Cons of Each Option

**Purpose:** Show your evaluation process. This section demonstrates rigorous thinking.

**Example:**
```markdown
## Pros and Cons of the Options

### Option 1: Redis (self-hosted)

- Good, because it supports complex data structures beyond key-value pairs (lists, sets, sorted sets)
- Good, because persistence options (RDB, AOF) prevent data loss on restarts
- Good, because excellent Python ecosystem with mature clients (redis-py, aioredis)
- Good, because active community and extensive documentation
- Good, because replication and Sentinel support for high availability
- Bad, because requires operational overhead (monitoring, backups, security patches)
- Bad, because in-memory storage limits cache size to available RAM
- Bad, because self-hosted means we manage infrastructure and failover

### Option 2: Memcached

- Good, because simple architecture is easier to understand and operate
- Good, because slightly lower memory overhead than Redis for pure key-value caching
- Good, because mature and battle-tested at large scale
- Bad, because no persistence; cache is lost on restart (cold cache problem)
- Bad, because limited to simple key-value storage (no lists, sets, etc.)
- Bad, because no built-in replication or high availability features
- Bad, because less active development compared to Redis

### Option 3: Application-level caching (Python `cachetools`)

- Good, because no external dependencies or infrastructure required
- Good, because zero operational overhead
- Good, because simplest possible solution
- Bad, because cache doesn't persist across application restarts
- Bad, because each application instance has its own cache (no sharing in multi-instance deployment)
- Bad, because limited by application memory; can't scale cache independently
- Bad, because no centralized cache invalidation across instances

### Option 4: Django cache framework with database backend

- Good, because uses existing database infrastructure (no new components)
- Good, because integrated with Django's ORM and view decorators
- Bad, because defeats the purpose; "caching to database" doesn't reduce database load
- Bad, because slower than in-memory caching solutions
- Bad, because adds complexity without meaningful performance benefit

### Option 5: Managed cache service (AWS ElastiCache)

- Good, because offloads operational burden (backups, patching, monitoring handled by AWS)
- Good, because automatic failover and high availability built-in
- Good, because can scale with a few clicks (no manual infrastructure changes)
- Bad, because costs $300-500/month for production-grade instances (exceeds budget)
- Bad, because vendor lock-in to AWS ecosystem
- Bad, because less control over configuration and troubleshooting
```

**Tips:**
- Use consistent structure: "Good, because..." and "Bad, because..."
- Evaluate each option against your decision drivers
- Be honest about weaknesses, even for your chosen option
- Avoid generic statements ("good performance"); be specific

## Step 7: Add Links and Relationships

**Purpose:** Connect this ADR to related decisions and external resources.

**Example:**
```markdown
## Links

- Related to [ADR-0005: Use PostgreSQL as primary database](0005-use-postgresql-as-primary-database.md)
- [Redis Documentation](https://redis.io/documentation)
- [redis-py Client Library](https://github.com/redis/redis-py)
- [AWS EC2 Instance Pricing](https://aws.amazon.com/ec2/pricing/)
- [Internal: Database Performance Analysis Report](https://internal-wiki/performance-analysis-2025-11)
```

**Tips:**
- Link to related ADRs (supersedes, related to, depends on)
- Include technical documentation references
- Add internal resources (wikis, reports, benchmarks) if applicable
- Keep links up-to-date; broken links reduce ADR value over time

## Step 8: Review and Get Feedback

Before marking the ADR as "accepted":

1. **Self-review checklist:**
   - Does the problem statement clearly explain *why* a decision is needed?
   - Are decision drivers specific and measurable?
   - Have I considered at least 3 viable alternatives?
   - Are pros/cons tied to actual project requirements (not generic)?
   - Do negative consequences show honest evaluation?
   - Is the justification clear enough that someone reading this in 2 years will understand?

2. **Share with the team:**
   ```bash
   # Preview the ADR in the web UI
   npx log4brains preview
   ```
   - Review in the Log4brains web interface for better readability
   - Share the URL with team members for feedback
   - Discuss in a technical design review or architecture meeting

3. **Update status to "accepted":**
   ```markdown
   - Status: accepted
   - Date: 2025-11-07
   ```

## Common Pitfalls to Avoid

### 1. Solution Disguised as Problem
**Bad:**
> Context: We need Redis.

**Good:**
> Context: Our database is overloaded with repetitive queries, causing 2.3s page load times.

### 2. Missing Justification
**Bad:**
> Decision Outcome: Use Redis.

**Good:**
> Decision Outcome: Use Redis because it provides persistence, rich data structures, and stays within our $200/month budget while meeting performance requirements.

### 3. Generic Pros/Cons
**Bad:**
> - Good, because it's fast
> - Bad, because it's complex

**Good:**
> - Good, because in-memory storage achieves <5ms latency for 95% of operations (vs. 100ms+ database queries)
> - Bad, because requires learning Redis-specific concepts (TTL, eviction policies, persistence modes) unfamiliar to our team

### 4. Choosing Without Evaluation
**Bad:** Only listing one option (the chosen one)

**Good:** List 3-5 realistic alternatives with honest pros/cons for each

### 5. Ignoring Negative Consequences
**Bad:** Only listing positive outcomes

**Good:** Acknowledge trade-offs, risks, and operational burdens introduced by the decision

## Next Steps

After creating your ADR:

1. **Commit it to version control:**
   ```bash
   git add docs/adr/YYYYMMDD-use-redis-for-application-caching.md
   git commit -m "Add ADR: Use Redis for application caching"
   ```

2. **Implement the decision** - ADRs document decisions, not replace implementation

3. **Update the ADR if circumstances change:**
   - If the decision is revisited, create a new ADR that supersedes this one
   - Don't delete old ADRs; they preserve historical context

4. **Reference the ADR in related work:**
   - Link to it in pull requests implementing the caching layer
   - Mention it in code comments explaining architectural choices

## Conclusion

Creating a good ADR takes 30-60 minutes, but pays dividends by:
- Preventing repeated debates about already-made decisions
- Onboarding new team members faster
- Providing context when revisiting decisions months or years later
- Demonstrating thoughtful evaluation of alternatives

The effort invested in documentation today saves hours of "why did we do it this way?" conversations tomorrow.
