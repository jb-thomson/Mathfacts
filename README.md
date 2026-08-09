# Numo — Math Fact Practice

A small, self-contained web app for practising addition, subtraction,
multiplication and division facts.

It adapts to the learner: facts unlock gradually, missed facts come back sooner
and more often, and facts count as mastered only once they can be recalled both
correctly and quickly. Each of the four sections has a landscape of its own that
fills in as it is practised, and is finished only when every fact in it is
mastered.

Built for phones and tablets, but it works fine with a keyboard on a desktop.

## Running it

Open `index.html`. That's the whole thing — there is no build step, no package
manager, and no server required. You can open it straight from the filesystem,
or drop the two files on any static host.

The app makes **no network requests at all**. Both fonts (Fredoka and Nunito)
are embedded in the file as base64 woff2, and every scene is drawn in SVG at
runtime rather than loaded as an image, which is why `index.html` is around
175 KB despite being a single page. Once the page has loaded it works offline.

`robots.txt` disallows everything and the page sends `noindex, nofollow,
noarchive`, so it stays out of search results.

## First run

Numo asks two things the first time it opens:

1. **A name**, so the app is visibly the child's own. It appears in the header,
   and a `Welcome back, NAME` banner fades in and out on each visit. It can be
   changed later from **Change name** at the bottom of the home screen.
2. **A world** — *Wild Places* or *Space*. The four sections get four different
   scenes either way, so the picture always says which kind of maths it belongs
   to.

**The world is chosen once and then fixed.** The picker says so plainly, and
nothing in the app reassigns it afterwards. Erasing all progress is the only way
to choose again.

## Choosing what to practise

The four buttons at the top — `+ − × ÷` — are toggles, and any combination
works. Pick one for a single-section session, or several to interleave them.
At least one always stays selected.

Points from each answer go to that operation's own scene, so a mixed session
grows several of them at once.

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
| **Mastered** | 5 correct in a row, on **5 different days**, averaging about 5 seconds |

Mastery is deliberately hard to reach, and the day requirement is the important
half. Three — or five — correct answers inside one sitting is short-term memory:
a child can be told `7 + 8 = 15` and echo it back minutes later. Requiring five
*separate days* means she has to still know it tomorrow, and next week. Cramming
cannot buy it.

The speed requirement is the other half. Recall that needs counting-on isn't
fluency, so a fact answered correctly but slowly stays in "learning". Because the
clock runs while she taps the answer in, longer answers get a matching
allowance — about a second more for two digits and two for three — so the bar
pays for typing, not for working it out.

A miss resets the streak but **keeps the banked days**: those are evidence built
up over weeks, and one bad morning shouldn't erase them.

Only the five most recent results and times are kept per fact, so old struggles
age out once a fact is genuinely known.

### Unlocking uses an easier bar than mastery

These are deliberately separate. If bands only opened once facts were *mastered*
under the rule above, a child would drill the same 16 starting facts for over a
month before seeing a new one. So a fact counts toward opening the next band as
soon as it's reliable — 3 correct in a row at a gentler speed — while mastery,
the fact map and the badge keep the strict rule. Practice keeps moving; finishing
still has to be earned.

### Mix

Each question picks a pool by weighted roll — roughly 45% favouring tricky
facts, 25% learning, 20% newly introduced, and 10% review of already-mastered
facts so they stay sharp. If the preferred pool is empty it falls through to the
next one. The fact just asked is skipped, unless it's the only one in its pool.

New facts are introduced at most 6 at a time, easiest first, so a beginner isn't
handed the whole unlocked band at once. This does mean the very first session
repeats a handful of facts several times, which is intentional — the pool is
small on purpose.

## The worlds

Every section has its own scene, and each grows something different so the four
never blur together:

| | Wild Places | Space |
| --- | --- | --- |
| **+** | Forest — young conifers | Rocky planet — habitat domes |
| **−** | Mountain meadow — wildflowers | Ice moon — comms masts |
| **×** | Beach — palms | Ring giant — tethered balloons |
| **÷** | Fjord — cabins along the shore | Station — docked pods |

Things appear one at a time and grow through three stages as more points come
in. Bigger pieces — a tree, a shrub, a pond, birds or satellites — arrive at
milestones, and the scene is comfortably full around 1000 points.

**Tap anything fully grown to change its colour.** That choice is remembered.

There is nothing to buy, place or arrange. A scene's layout comes from a seed
derived from its world and section, so it is stable between visits, different
from its neighbours, and impossible to leave in a broken state — none of it
needs saving.

## Two meters, doing different jobs

Each section shows two, and the split is deliberate:

- **The bar inside the scene** tracks **points**. It moves every session and
  shows how full the scene is.
- **The meter below it** tracks **facts mastered** — "84 of 169 facts mastered".
  It moves slowly, and it is the one that decides when a section is *finished*.

Early on the mastery meter reads close to empty. That's honest: the points bar
is what carries short-term motivation, which is exactly why both are there.

## Finishing a section

A section is complete when **every fact in it is mastered** — 169 facts for
`+`, `−` and `×`, and 156 for `÷`. When the last one falls:

- **The scene transforms.** It shifts to warm golden-hour light and gains a
  landmark it never had before — a stag in the forest, a lit lighthouse on the
  beach, a rainbow over the meadow, a boat on the fjord, a planted flag in
  Space.
- **A badge** appears beside the section's name and is **never taken away**. If a
  fact later slips out of mastery the badge turns amber and the note says how
  many need a refresh — a warning to act on, not an achievement snatched back.
  It returns to gold once they're fixed.
- **The session summary announces it**, and the home screen counts how many of
  the four are done.

### How long this actually takes

Simulating a child practising one session a day, with the real scheduling and
mastery code:

| | Addition | Multiplication |
| --- | --- | --- |
| A child who picks facts up steadily | ~74 days | ~95 days |
| A child who is still missing flashcards | ~185 days | ~246 days |

So somewhere between two and eight months of daily practice per section. That is
the point: it takes as long as it takes to actually know them. New bands still
open within the first week, so practice never feels stuck while that plays out.

## Progress and streaks

The home screen shows the four scenes with their points and mastery, a fact map
per section (tap any dot for that fact's history), and the last 10 days of
practice.

A streak continues from yesterday, so practising later in the day doesn't break
it — but only completed sets count, as described above.

**Erase all progress** requires two taps — the first arms it, and it disarms
itself after 3 seconds if you don't confirm. It also clears the name and world,
so setup runs again.

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
  v: 4,
  name:     "Alex",                             // "" until asked
  world:    "wild",                             // "wild" | "space" — set once, then fixed
  pts:      { add:0, sub:0, mul:0, div:0 },     // points per section, fills the scenes
  ops:      ["add","sub"],                      // sections selected to practise
  unlocked: { add:3, sub:2, mul:2, div:2 },     // highest band unlocked per section
  facts: {
    add: { "3,4": { n, s, r: [], t: [], d: [] } },   // keyed "a,b"
    sub: {}, mul: {}, div: {}
  },
  blooms:     { add: { "3": 2 }, sub:{}, mul:{}, div:{} },  // recoloured growers
  finishedOn: { add: "2026-08-09" },            // first completion; never cleared
  days:       { "2026-08-09": 1 },              // sessions completed per day
  sound:      true
}
```

Per fact: `n` total attempts, `s` current correct streak, `r` last five results
as 1/0, `t` last five correct response times in milliseconds, and `d` the
separate days it has been answered correctly on — the thing cramming can't fake.

Mastery counts and completion are **derived** from `facts` rather than stored,
so they can never fall out of step with the real history.

Older saves migrate automatically on load, and the upgraded save is written
immediately so the migration survives even if the app is closed straight away:

- **v3** (before the day requirement) keeps everything. Facts that met the old,
  easier bar are credited with **3 of the 5 days** now required, so real work
  isn't erased but the too-easy mastery isn't grandfathered in either.
- **v2** (Numo before worlds) keeps everything — points, facts, bands, days,
  recoloured growers, sound — and is simply asked for a name and a world.
- **v1** (`plusminus.v1`, the original Plus & Minus) carries over facts, bands,
  days and sound. Its single points total predates per-operation scoring, so it
  is split between the addition and subtraction scenes in proportion to how much
  of each was actually practised. The old key is left in place as a fallback.

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
| `GOAL` | 1000 | Points for a comfortably full scene |
| `SESSION_LEN` | 20 | Questions per session |
| `SESSION_MAX` | 24 | Ceiling when re-asking missed facts |
| `FAST_MS` | 4000 | Speed-bonus threshold |
| `MASTER_MS` | 5000 | Base recall time for mastery (longer answers get more) |
| `MASTER_STREAK` | 5 | Correct answers in a row required |
| `MASTER_DAYS` | 5 | Separate days a fact must be right on |
| `UNLOCK_MS` / `UNLOCK_STREAK` | 7000 / 3 | The gentler bar that opens new bands |
| `RECENT_N` | 5 | How many recent results and times are kept per fact |

Scene pacing lives beside them: `slotThreshold(i)` sets when the `i`th thing
appears, `BUD_AT` and `BLOOM_AT` how quickly each grows up, and the `MILE` table
when the bigger pieces arrive. Colours and layers for all eight scenes are in
the `SCENE` table, and `WORLDS` maps sections to scenes. Changing any of it
restyles every scene immediately — no saved data depends on it.

Lowering `MASTER_MS` makes mastery stricter; raising `SESSION_LEN` makes
sessions longer. Changing `LSKEY` starts everyone from scratch.
