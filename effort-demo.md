# Reasoning-effort demo - same prompt, low vs high

Run this identical prompt twice: once at **low** effort, once at **high**. Low effort
often returns a plan that breaks a constraint or under-optimizes; high effort works the
constraints together and finds the real optimum. The gap is the whole argument for the dial.

> Note: some models get it right even at low effort. That is fine - the point is that
> low effort is *not reliable* here, so a task with real constraints is where you raise it.

## The prompt (paste as-is)

```
You are planning a campaign. Choose exactly five campaigns.

Constraints:
- Total spend must be at most $65K
- Total reach must be at least 1.80M
- Include at least two B2B and at least two B2C campaigns
- Use no channel more than twice

Primary objective: maximize qualified leads.
Tie-breaker: if two plans have the same number of qualified leads, choose the one
with higher reach.

Return only: campaign IDs | spend | reach | qualified leads

ID | Spend ($K) | Reach (K) | Qualified leads | Audience | Channel
A  | 12 | 340 | 42 | B2B | Search
B  | 15 | 450 | 55 | B2C | Social
C  |  9 | 260 | 36 | B2B | Email
D  | 18 | 500 | 64 | B2C | Video
E  | 11 | 310 | 40 | B2B | Social
F  | 14 | 390 | 50 | B2C | Search
G  |  8 | 180 | 25 | B2B | Webinar
H  | 16 | 470 | 60 | B2C | Social
I  | 10 | 290 | 38 | B2B | Search
J  | 13 | 360 | 47 | B2C | Webinar
K  |  7 | 220 | 31 | B2B | Email
L  | 17 | 480 | 62 | B2C | Video
```

## Correct answer

```
B, H, I, K, L | $65K | 1.91M | 246
```

Check it against every constraint:
- **Spend:** 15 + 16 + 10 + 7 + 17 = **$65K** (<= 65) ✓
- **Reach:** 450 + 470 + 290 + 220 + 480 = **1.91M** (>= 1.80M) ✓
- **B2B:** I, K = 2 (>= 2) ✓  ·  **B2C:** B, H, L = 3 (>= 2) ✓
- **Channels:** Social (B, H) x2, Search (I) x1, Email (K) x1, Video (L) x1 - none over twice ✓
- **Qualified leads:** 55 + 60 + 38 + 31 + 62 = **246** (the maximum that satisfies all constraints)

## How to run it

1. Set low effort (`/effort low` in Codex, or the tool's setting). Paste the prompt. Note the answer and whether it satisfies every constraint.
2. Fresh chat. Set high effort (`/effort high`). Paste the same prompt.
3. Compare: did low effort break a constraint (over $65K, a channel three times) or return fewer than 246 leads? Did high effort reach 246 and stay legal?

## The takeaway

A single missed constraint makes a confident-looking answer wrong. On tasks with several
constraints and a real objective, raise the effort - and always verify the answer against
the constraints yourself, whatever effort produced it.
