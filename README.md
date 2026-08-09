# Numo — Math Fact Practice

A small, self-contained web app for practising addition, subtraction,
multiplication and division facts.

It adapts to the learner: facts unlock gradually, missed facts come back sooner
and more often, and facts count as mastered only once they can be recalled both
correctly and quickly. Each of the four sections grows a garden of its own —
the more points earned in a section, the fuller and busier its garden gets.

Built for phones and tablets, but it works fine with a keyboard on a desktop.

## Running it

Open `index.html`. That's the whole thing — there is no build step, no package
manager, and no server required. You can open it straight from the filesystem,
or drop the two files on any static host.

The app makes **no network requests at all**. Both fonts (Fredoka and Nunito)
are embedded in the file as base64 woff2, which is why `index.html` is around
145 KB despite being a single page. Once the page has loaded it works offline.

`robots.txt` disallows everything and the page sends `noindex, nofollow,
noarchive`, so it stays out of search results.

## Choosing what to practise

The four buttons at the top — `+ − × ÷` — are toggles, and any combination
works. Pick one for a single-section session, or several to interleave them.
At least one always stays selected.

Points from each answer go to that operation's own garden, so a mixed session
waters several gardens at once.

## How a session works

Tap **Start practicing** and answer 20 questions on the number pad. A session
can stretch to 24 questions if there are missed facts still waiting to be
re-asked.

- **Correct** — 5 points, plus 3 more if you answered within 4 seconds.
- **Newly mastered** — a 20 point bonus and a star.
- **Wrong** — the correct answer is shown, and the fact is queued to come back
  about 3 questions later, within the same session.

Getting a previously-missed fact right doesn't immediately clear it. It comes
back once more, roughly 4 questions later, to confirm it stuck.

Input works by tapping the pad, or with the keyboard: digits to enter,
`Backspace` to delete, `Enter` to submit. Answers can be up to three digits,
since products reach 144.

### Finishing counts

A practice day is earned by **working through the whole set**. Stopping partway
keeps the points already earned, but doesn't fill in the day or extend the
streak, and the summary says so.

Stopping takes two taps of the × button — the first arms it and warns what will
happen, and it disarms itself after 3 seconds. A stray tap can't end a set the
child was most of the way through.

## Which facts get asked

Facts are built so every answer stays within 0–12:

| Section | Question | Answer |
| --- | --- | --- |
| Addition | `a + b` | up to 24 |
| Subtraction | `(a + b) − b` | `a` |
| Multiplication | `a × b` | up to 144 |
| Division | `(a × b) ÷ b` | `a` |

`a` and `b` run from 0 to 12, except for division, where `b` starts at 1 —
nothing is ever divided by zero.

### Unlocking

Facts are grouped into bands by their larger operand, `max(a, b)`. A new learner
starts with addition up to band 3 and the other three sections up to band 2. The
next band unlocks once **80% of everything currently unlocked is mastered**, so
the pool widens only when the current pool is genuinely solid. Each section
unlocks independently.

### Status

Every fact is in one of five states, shown as a dot on the fact map:

| Status | Meaning |
| --- | --- |
| **Locked** | In a band that hasn't unlocked yet |
| **New** | Unlocked but never practised |
| **Learning** | Practised, not yet mastered |
| **Tricky** | Missed last time, or missed at least twice in the last five tries |
| **Mastered** | At least 3 correct in a row, *and* the last 3 correct answers averaged 7 seconds or less |

The speed requirement is deliberate. Recall that needs counting-on isn't
fluency, so a fact answered correctly but slowly stays in "learning".

Only the five most recent results and times are kept per fact, so old struggles
age out once a fact is genuinely known.

### Mix

Each question picks a pool by weighted roll — roughly 45% favouring tricky
facts, 25% learning, 20% newly introduced, and 10% review of already-mastered
facts so they stay sharp. If the preferred pool is empty it falls through to the
next one. The fact just asked is skipped, unless it's the only one in its pool.

New facts are introduced at most 6 at a time, easiest first, so a beginner isn't
handed the whole unlocked band at once. This does mean the very first session
repeats a handful of facts several times, which is intentional — the pool is
small on purpose.

## The gardens

Each section has a garden that fills in on its own as its points rise. There is
nothing to buy, place or arrange: the picture is a pure function of the points
earned, so it can't end up empty-looking or badly arranged, and it always
reflects real practice.

A flower appears every so often, sprouting, budding and then blooming as more
points come in. Along the way a shrub, a tree, a pond, butterflies and finally a
rainbow arrive at milestones. Each section's garden is laid out from a seed
derived from its name, so the four look distinct but never change shape between
visits.

**Tap any bloomed flower to change its colour.** That choice is remembered.

A garden reaches full bloom at 1000 points, and the occasional extra visitor
still turns up beyond that.

## Progress and streaks

The home screen shows the four gardens with their points, a fact map per
section (tap any dot for that fact's history), and the last 10 days of practice.

A streak continues from yesterday, so practising later in the day doesn't break
it — but only completed sets count, as described above.

**Erase all progress** requires two taps — the first arms it, and it disarms
itself after 3 seconds if you don't confirm.

## Sound and haptics

All sound is synthesised with the Web Audio API; there are no audio files.
Vibration fires only where the browser supports it, which in practice means
Android — iOS has no web haptics.

iOS also mutes audio until it's started from inside a real user gesture, so the
app plays a silent buffer on the first touch to unlock it. The speaker button in
the quiz header toggles both sound and vibration, and the choice is remembered.

Animations are disabled automatically under `prefers-reduced-motion`.

## Saved data

Everything lives in `localStorage` under the key `numo.v1`, on the device only.
Nothing is uploaded or shared. If storage is unavailable — Safari private
browsing, for instance — the app falls back to keeping state in memory for the
session rather than failing.

```js
{
  v: 2,
  pts:      { add:0, sub:0, mul:0, div:0 },    // points per section, drives the gardens
  ops:      ["add","sub"],                      // sections selected to practise
  unlocked: { add:3, sub:2, mul:2, div:2 },     // highest band unlocked per section
  facts: {
    add: { "3,4": { n, s, r: [], t: [] } },     // keyed "a,b"
    sub: {}, mul: {}, div: {}
  },
  blooms:   { add: { "3": 2 }, sub:{}, mul:{}, div:{} },  // recoloured flowers
  days:     { "2026-08-09": 1 },                // sessions completed per day
  sound:    true
}
```

Per fact: `n` total attempts, `s` current correct streak, `r` last five results
as 1/0, `t` last five correct response times in milliseconds.

Saves from the earlier Plus & Minus version (`plusminus.v1`) are migrated
automatically on first load. Facts, unlocked bands, practice days and the sound
setting carry over as they are; the old single points total is split between the
addition and subtraction gardens in proportion to how much of each was actually
practised, since points were never recorded per operation back then. The old key
is left in place as a fallback.

Clearing site data resets progress, as does the erase button.

## Layout

```
index.html    the entire app — markup, styles, fonts, and logic
robots.txt    keeps the app out of search engines
```

## Tuning

The constants that shape the difficulty curve are at the top of the script in
`index.html`:

| Constant | Default | Controls |
| --- | --- | --- |
| `MAX` | 12 | Largest operand |
| `GOAL` | 1000 | Points for one garden in full bloom |
| `SESSION_LEN` | 20 | Questions per session |
| `SESSION_MAX` | 24 | Ceiling when re-asking missed facts |
| `FAST_MS` | 4000 | Speed-bonus threshold |
| `MASTER_MS` | 7000 | Average recall time required for mastery |
| `RECENT_N` | 5 | How many recent results and times are kept per fact |

Garden pacing lives beside them: `flowerThreshold(i)` sets when flower `i`
appears, `BUD_AT` and `BLOOM_AT` how quickly each one opens, and the `MILE`
table when the shrub, tree, pond, butterflies and rainbow arrive. Changing any
of these restyles every garden immediately — no saved data depends on them.

Lowering `MASTER_MS` makes mastery stricter; raising `SESSION_LEN` makes
sessions longer. Changing `LSKEY` starts everyone from scratch.
