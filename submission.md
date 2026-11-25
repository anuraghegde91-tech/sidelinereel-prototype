Video: [VIDEO_LINK]
Prototype: https://tatsyatech.github.io/sidelinereel-prototype/
Submitted for the Ajaia PM assessment — https://ajaia.ai

## Task 1 – Roadmap call

**Build next (ranked):**

1. **Roster tagging accuracy for look-alike jersey numbers.** Evidence: M3 – 3 tickets, 3 clubs, 2 sports, same failure (14 vs 4, 1 vs 11, swapped digits), one from Ridgeline; M5 – wrong-kid thumbnails are a stated cause of the ~30% of reels never opened.
2. **Honest headline metric: share rate over ALL generated reels** (~64% = 92% x ~70% opened, my estimate) instead of 92% over opened reels only (M5). Near-zero effort; it is the success metric for #1.
3. **Daytime notification send time** (M5: late-night delivery is the other cause of unopened reels).
4. **Research why the Reel Editor is unused** before building more editing controls (M2: 58% ask, 6% open, <1% of openers finish an edit; behaviour contradicts the survey).

**Not now:**

- **Livestreaming:** one account asking (M1 and M4 are the same signal), outside the core job (highlights after the game), Carla herself credits reels for renewals, and Ridgeline has its own mis-tag ticket. Next step is a discovery call with her board, not a build.
- **More editing controls:** behaviour contradicts survey (M2).
- **App icon:** cosmetic. **Missing watermark:** real but minor bug, goes to the bug queue.

**Evidence vs urgency:** Ridgeline's $180k raises urgency, not evidence of user need.

**Message to Carla (via Jamal):**

> Carla, your reels are what you told us drove half your renewals, so we're putting those first. A Ridgeline parent reported a clip of the wrong kid this week; we've seen the same issue at other clubs, mostly when jersey numbers look alike. We're shipping a fix where coaches confirm uncertain tags before reels go out, and Ridgeline gets it first, before your signups open. Livestreaming isn't on the next 6-week roadmap, and I'd rather tell you that than promise something rushed. I'd like to meet you and your board, at your availability, to understand who needs to watch live and what would be enough, then give you a clear yes or no on timing.

## Task 2 – Spec: Coach-confirmed tagging for uncertain clips

### Problem
Wrong kid's clip lands in a reel when jersey numbers look alike (M3, M5).

### Outcome
Fewer wrong-kid clips reach families; more generated reels opened and shared.

**Success metrics:**
- (a) "Not my kid" reports per 1,000 clips sent – down
- (b) Share rate over all generated reels – up from ~64% baseline
- (c) Open rate – up

Target numbers for each metric are hypotheses to be set with data/eng, not commitments.

### In scope
1. Flag a clip as uncertain if tag confidence < threshold (start 0.8, configurable) OR detected number is a look-alike of another roster number on the same team (substring pairs like 4/14, 1/11; digit swaps like 16/61).
2. Coach review queue before weekly send, showing thumbnail, detected number, reason, top 2 candidate kids; actions Confirm / Reassign / Remove.
3. Uncertain clips the coach has not reviewed by send time are held out of reels, never sent unconfirmed.
4. Parent "Not my kid" button per clip – hides clip for that family, sends it to coach queue.
5. Log every coach decision and parent report for retraining/eval.

### Out of scope
Livestreaming; new editing controls; retraining the vision model in this release; notification timing (separate item #3); icon and watermark.

### Human in the loop
Coach decides every uncertain tag; parent can flag any sent clip; the system never sends an uncertain clip without a coach decision.

### Acceptance criteria (Given/When/Then, testable)

- **Given** a clip with a confidence ≥ 0.8 whose detected number is a look-alike of another roster number, **when** the clip is processed, **then** it is flagged as uncertain regardless of its confidence score.
- **Given** a clip with confidence 0.79, **when** it is processed, **then** it is flagged as uncertain; **given** a clip with confidence 0.80, **when** it is processed, **then** it is not flagged for confidence alone.
- **Given** an uncertain clip in the coach queue, **when** the coach selects Confirm, **then** the clip is assigned to the confirmed kid and removed from the queue.
- **Given** an uncertain clip in the coach queue, **when** the coach selects Reassign, **then** the clip is moved into the chosen kid's reel.
- **Given** an uncertain clip in the coach queue, **when** the coach selects Remove, **then** the clip is excluded from all reels.
- **Given** an uncertain clip that is unreviewed at send time, **when** reels are sent, **then** the clip is excluded and the coach sees a count of clips held.
- **Given** a sent clip, **when** a parent selects "Not my kid", **then** the clip is hidden for that family within the session and added to the coach queue.
- **Given** any state of the coach queue, **when** the queue is viewed, **then** the counter and empty state reflect the correct remaining count.
- **Given** any coach decision or parent report, **when** it occurs, **then** it is logged with user, time, and action.

### Top 2 failure modes and what catches each

1. **Coaches don't review the queue** → clips held, reels thinner, kids missing moments.
   Caught by: % of queued clips reviewed before send, per club; % clips held. Alert if a club's review rate < 50% two weeks running; then tune threshold or add a reminder.
2. **Confidently wrong tags the model never flags** → wrong kid still reaches families.
   Caught by: "Not my kid" rate per club and per number pair; weekly audit sample of high-confidence clips. Rising rate on a pair adds it to the look-alike list.

### Open questions for engineering
- Default threshold from current confidence distribution.
- Expected queue size per team per week (target: under 5 minutes of coach time — hypothesis).

## Task 3 – Prototype

**Public link:** https://tatsyatech.github.io/sidelinereel-prototype/

**How to run:** Open `index.html` in any browser (no build step, no backend, works offline by double-clicking the file) or visit the public link above. A "Coach / Parent" toggle in the top-right switches between the two views for demo purposes.

**Click path:**
Review queue → clip detail → Confirm / Reassign / Remove → Ready to send (7 PM default, honest metric) → Send → Parent view → "Not my kid"

## Task 4 – AI workflow note

[I WILL WRITE THIS]
