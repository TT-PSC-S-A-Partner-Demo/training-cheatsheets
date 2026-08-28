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

---

## Extended version (harder - 6 of 16, with dependencies)

A tougher alternative: more campaigns, an exact-count constraint, and if-then dependencies,
so low effort trips far more often. Verified by brute force - the answer below is the
**global optimum**.

### The prompt (paste as-is)

```
You are planning a marketing portfolio. Select exactly six campaigns.

Constraints:
- Total spend must be $82K or less
- Total reach must be at least 2.15M
- Select at least three B2B and at least two B2C campaigns
- Use no channel more than twice
- Select exactly one Experimental campaign
- Campaigns B and L cannot both be selected
- If campaign J is selected, campaign H must also be selected
- If campaign P is selected, campaign C must also be selected

Objective: 1) maximize qualified leads; 2) if tied, maximize reach; 3) if still tied, minimize spend.
Return only: {"campaign_ids":["..."],"spend_k":0,"reach_k":0,"qualified_leads":0}

ID | Spend($K) | Reach(K) | Leads | Audience | Channel | Type
A 11 310 34 B2B Search Core        I 10 280 35 B2C Webinar Experimental
B 15 440 51 B2C Social Core        J 16 460 56 B2C Video Core
C  8 240 31 B2B Email Core         K  7 190 26 B2B Email Core
D 17 490 59 B2C Video Core         L 18 510 61 B2C Social Core
E 13 360 43 B2B Social Core        M 11 340 44 B2B Search Experimental
F 14 400 48 B2C Search Core        N 15 420 50 B2C Webinar Core
G  9 220 28 B2B Webinar Experimental O 12 350 41 B2B Social Core
H 12 330 40 B2B Email Core         P 19 540 65 B2C Video Experimental
```

### Correct answer (global optimum)

```
{"campaign_ids":["C","D","H","J","L","M"],"spend_k":82,"reach_k":2370,"qualified_leads":291}
```

- **Count:** 6 ✓  ·  **Spend:** 8+17+12+16+18+11 = 82 (&le;82) ✓  ·  **Reach:** 240+490+330+460+510+340 = 2370 (&ge;2150) ✓
- **B2B:** C,H,M = 3 (&ge;3) ✓  ·  **B2C:** D,J,L = 3 (&ge;2) ✓
- **Channels:** Email C,H x2; Video D,J x2; Social L x1; Search M x1 - none over twice ✓
- **Experimental:** M only = exactly 1 ✓
- **Deps:** J in and H in ✓ · B not selected, so not with L ✓ · P not selected ✓
- **Leads:** 31+59+40+56+61+44 = 291 - the maximum that satisfies every constraint ✓

### How to run it

1. `/effort low`, paste, note whether the answer satisfies **every** constraint.
2. Fresh chat, `/effort high`, paste the same prompt.
3. Compare: did low break something - over **$82K**, a channel 3x, not exactly one
   Experimental, or a violated dependency - or score **under 291**? Did high hit 291 and
   stay legal? The dependencies are where low effort fails most.
