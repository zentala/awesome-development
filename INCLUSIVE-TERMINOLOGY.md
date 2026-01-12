# Inclusive Terminology in Tech

**Inclusive Terminology** refers to using technical terms that avoid negative associations with enslavement, social hierarchies, or violence. It's not just about political correctness—it's about clarity, precision, and following modern industry standards.

## Why It Matters

Since 2020, major tech companies and open-source projects have adopted inclusive terminology:
* **GitHub (2020)** - Changed default branch from `master` to `main`
* **Python (2021)** - Removed master/slave terminology from documentation
* **Redis (2020)** - Changed master/slave to primary/replica
* **MySQL (2020)** - Same transition to primary/replica
* **Drupal (2020)** - Replaced master/slave with primary/replica
* **Django (2020)** - Replaced master/slave in documentation
* **Most Fortune 500 tech companies** - Now mandate this in their style guides

## Technical Benefits

This isn't just social activism—there are real technical advantages:

✅ **Clearer Meaning**
- `primary/replica` more precisely describes the relationship than `master/slave`
- `allowlist/blocklist` explicitly states what the list does
- `terminate` is more accurate than `kill` for processes

✅ **Better Internationalization**
- Easier to translate into other languages
- Fewer cultural issues in global teams
- Reduced confusion in non-English speaking countries

✅ **Industry Standard**
- New projects and frameworks use these terms
- Old terms trigger linter warnings in modern tools
- Following standards improves code maintainability

✅ **Future-Proof**
- Code won't need updating for compliance
- Aligns with evolving industry best practices
- Reduces technical debt

## Old vs New Terms

| ❌ Avoid | ✅ Use Instead | Why? | Example Context |
|----------|----------------|------|-----------------|
| **master/slave** | **primary/replica** | "slave" references human enslavement | Database replication |
| **master/slave** | **leader/follower** | Same issue, different context | Distributed systems |
| **master/slave** | **primary/secondary** | Same issue, alternative | Load balancing |
| **whitelist/blacklist** | **allowlist/blocklist** | white=good, black=bad has racial connotations | Access control, firewalls |
| **master branch** | **main branch** | Same as master/slave issue | Git repositories |
| **kill process** | **terminate/stop process** | "kill" = violence | Process management |
| **abort** | **cancel/stop** | "abort" = violent termination | Operations, transactions |
| **sanity check** | **validation/confidence check** | "sanity" stigmatizes mental health | Testing, QA |
| **dummy value** | **placeholder/example/mock** | "dummy" = insulting | Test data, examples |
| **native/foreign key** | **local/remote key** | "foreign" can be exclusionary | Database design |
| **man hours** | **person hours/work hours** | Gendered language | Project estimation |
| **chairman** | **chair/chairperson** | Gendered language | Meetings, governance |

## Code Examples

### Database Replication

**Before:**
```javascript
const masterDatabase = connectToMaster();
const slaveDatabase = connectToSlave();

if (!masterDatabase.isHealthy()) {
  failoverToSlave(slaveDatabase);
}
```

**After:**
```javascript
const primaryDatabase = connectToPrimary();
const replicaDatabase = connectToReplica();

if (!primaryDatabase.isHealthy()) {
  failoverToReplica(replicaDatabase);
}
```

### Access Control

**Before:**
```python
whitelist = ['192.168.1.1', '10.0.0.1']
blacklist = ['malicious.com', 'spam.net']

def is_allowed(ip):
    if ip in blacklist:
        return False
    return ip in whitelist
```

**After:**
```python
allowlist = ['192.168.1.1', '10.0.0.1']
blocklist = ['malicious.com', 'spam.net']

def is_allowed(ip):
    if ip in blocklist:
        return False
    return ip in allowlist
```

### Git Branching

**Before:**
```bash
git checkout master
git merge feature-branch
git push origin master
```

**After:**
```bash
git checkout main
git merge feature-branch
git push origin main
```

### Process Management

**Before:**
```go
func killProcess(pid int) error {
    return syscall.Kill(pid, syscall.SIGTERM)
}

// Sanity check before killing
if pid > 0 {
    killProcess(pid)
}
```

**After:**
```go
func terminateProcess(pid int) error {
    return syscall.Kill(pid, syscall.SIGTERM)
}

// Validation check before terminating
if pid > 0 {
    terminateProcess(pid)
}
```

### Distributed Systems

**Before:**
```java
public interface ClusterNode {
    boolean isMaster();
    void promoteToMaster();
    void demoteToSlave();
}
```

**After:**
```java
public interface ClusterNode {
    boolean isLeader();
    void promoteToLeader();
    void demoteToFollower();
}
```

## Implementation Guidelines

### For New Projects

✅ **Use inclusive terms from day one**
- Set Git default branch to `main`
- Use `primary/replica` in database configs
- Use `allowlist/blocklist` in access control
- Use `terminate/stop` for process management

```bash
# Set default branch name for new repos
git config --global init.defaultBranch main
```

### For Legacy Code

**Don't rush to change everything at once.** Update gradually:

✅ **High Priority** (change when possible):
- Public APIs and documentation
- User-facing messages
- New features and major refactors

⏸️ **Medium Priority** (change during normal maintenance):
- Internal function names
- Variable names in active development
- Comments and code documentation

⏳ **Low Priority** (change opportunistically):
- Legacy code that rarely changes
- Third-party integrations (if they use old terms)
- Internal tools with no external impact

**Example Refactoring Strategy:**
```javascript
// Step 1: Add new function, keep old one
function connectToPrimary() { /* new implementation */ }
function connectToMaster() {
  // @deprecated Use connectToPrimary() instead
  return connectToPrimary();
}

// Step 2: Update all new code to use connectToPrimary()

// Step 3: Eventually remove connectToMaster() in next major version
```

### Documentation

**Update when next revising docs:**
- README files
- API documentation
- Architecture diagrams
- Onboarding guides
- Wiki pages

### Code Reviews

**Suggest inclusive alternatives when reviewing PRs:**
- "Consider using `allowlist` instead of `whitelist`"
- "Let's use `primary/replica` instead of `master/slave`"
- Keep feedback constructive and educational

## Common Questions

### Q: Is this just "political correctness"?

**A:** No. Major tech companies adopted these changes for technical clarity and international collaboration. The terms `primary/replica` are more descriptive than `master/slave`. It's good engineering.

### Q: Won't this break existing code?

**A:** Only if you change APIs without deprecation. Use gradual migration:
1. Add new terms alongside old ones
2. Mark old terms as deprecated
3. Update documentation
4. Remove old terms in next major version

### Q: What about third-party libraries?

**A:** You don't need to change third-party code. Use inclusive terms in **your** code and documentation. Over time, libraries will update on their own schedules.

### Q: Should I rename my `master` branch?

**A:** For new projects, yes—use `main`. For existing projects, it's optional but recommended. GitHub provides migration guides.

```bash
# Rename master to main
git branch -m master main
git push -u origin main

# Update default branch on GitHub, then:
git push origin --delete master
```

### Q: Does this apply to non-English projects?

**A:** Yes. These terms are often used in English even in non-English codebases, and the same concerns apply.

## Real-World Adoption

### GitHub Default Branch

GitHub changed default branch from `master` to `main` in 2020. Stats:
- 75%+ of new repos use `main` (as of 2023)
- Most major open-source projects migrated
- GitHub provides automated migration tools

### Database Systems

**Before (Redis 5.x):**
```
redis-cli info replication
role:master
connected_slaves:2
```

**After (Redis 6.x+):**
```
redis-cli info replication
role:master  # Still shows "master" for backward compatibility
connected_replicas:2  # But uses "replicas" in new terminology
```

**MySQL 8.0.26+:**
- Removed `SHOW SLAVE STATUS`
- Added `SHOW REPLICA STATUS`
- Deprecated `START SLAVE`, added `START REPLICA`

## Resources

### Official Guides

* [GitHub Renaming Guide](https://github.com/github/renaming) - How to rename your default branch
* [Google Developer Style Guide](https://developers.google.com/style/inclusive-documentation) - Inclusive documentation
* [Microsoft Writing Style Guide](https://docs.microsoft.com/en-us/style-guide/bias-free-communication) - Bias-free communication
* [IETF Terminology RFC 8989](https://datatracker.ietf.org/doc/html/rfc8989) - Official terminology recommendations
* [Python's Inclusive Naming Initiative](https://github.com/python/cpython/issues/82254)

### Tools

* [Alex](https://github.com/get-alex/alex) - Linter for insensitive, inconsiderate writing
* [woke](https://github.com/get-woke/woke) - Detects non-inclusive language in your code
* [GitHub Actions for inclusive terminology](https://github.com/marketplace/actions/linter-for-common-words)

### Reading

* [Changing Redis Master-Slave Replication Terms](https://github.com/redis/redis/issues/5335)
* [MySQL Terminology Updates](https://dev.mysql.com/doc/refman/8.0/en/replication.html)
* [IETF on Inclusive Language](https://www.ietf.org/about/groups/iesg/statements/on-inclusive-language/)

## Summary

Inclusive terminology is:
- ✅ **More precise**: Better describes technical relationships
- ✅ **Industry standard**: Adopted by major tech companies
- ✅ **Future-proof**: Aligns with evolving best practices
- ✅ **Professional**: Shows attention to modern development standards

**Start using inclusive terms today.** Your future self (and your international colleagues) will thank you.
