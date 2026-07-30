---
layout: legal
title: Abide User Guide
permalink: /abide/guide/
---
<!-- Source of truth: abide repo docs/guide/abide-guide.md. Keep in sync on guide edits. -->

# Abide — User Guide

Abide is a calm, on-device toolset for hiding Scripture in your heart and
keeping a prayer journal. Everything you write stays on your device. This
guide explains how each part works — and, just as important, *why* a few
things behave the way they do.

## Overview

Abide has three pillars:

- **Memorization** — learn verses with fill-in-the-blank drills, then keep
  them with spaced repetition.
- **Prayer** — a simple, private prayer journal with a gentle daily queue.
- **Study** — read a passage with notes, cross-references, commentary, and the
  original Hebrew/Greek.

Memorization and Prayer are native on macOS and iPhone/iPad. Study is available
in the app and on the web.

Memorization has **two phases** on purpose: an intensive *learning* phase to get
a verse into your head, then a low-frequency *reviewing* phase to keep it there.
Understanding that split explains most of how the app behaves.

## Memorization

**Cloze drills.** "Cloze" (rhymes with *froze*) means fill-in-the-blank: Abide
hides some of the verse's words and you recall them. The percentage on a card
is the **fraction of words blanked out** — 25%, 50%, 75%, 100% — *not* a score.
Higher percent = harder.

**Spaced repetition (SM-2).** Once you know a verse, Abide schedules it to come
back at growing intervals — a day, then several days, then weeks — so you review
it just before you'd forget. Each review you rate Again / Hard / Good / Easy,
and that rating adjusts when the verse returns.

**Why a freshly-activated verse keeps coming back (this is not a bug).** A card
in the **learning** phase has *no once-per-day limit*. Abide keeps serving it
within the same session, climbing 0 → 25 → 50 → 75 → 100%, and it only leaves
the queue once you reach 100% **and** rate it Good or Easy. So if you add a
verse, review it once, and expect it to disappear for the day — it won't, and
that's by design. Only **reviewing** (spaced-repetition) cards are limited to
once per day.

## Glossary

| Term | What it means |
|---|---|
| **Cloze** | Fill-in-the-blank recall. The % is the share of words hidden, not a grade. |
| **Learning** | The first phase — cloze drills that repeat until the verse is solid. |
| **Reviewing** | The second phase — spaced-repetition upkeep, at most once per day per card. |
| **SRS / SM-2** | "Spaced Repetition System"; SM-2 is the specific scheduling algorithm Abide uses. |
| **Interval** | How many days until a reviewing card is due again. |
| **Ease** | A per-card factor that grows/shrinks the interval based on how easily you recall it. |
| **Backlog / Park** | The same "set aside" state under two labels. **Park** (in Library) moves an active card to the **Backlog** — out of the learning queue and off the review schedule until you activate it again. |

## What every Stats number counts

The Stats page summarizes your library. A few numbers are easy to misread:

| Number | What it counts |
|---|---|
| **Total cards** | Every verse in your library across all states. |
| **In learning (cloze)** | Cards in the learning phase, still being drilled. |
| **Reviewing** | Cards that have graduated to spaced repetition. |
| **Backlog** | Parked cards, excluded from both queues. |
| **Due today** | **Reviewing cards only** that are scheduled for today. |

**The one that trips people up:** "Due today" counts *reviewing* cards only.
Learning cards have no due date, so they never add to it. That means **Stats can
say "Due today: 0" while the Review screen still has a card waiting** — that
card is a *learning* card, counted under "In learning (cloze)". Zero due does
not mean nothing to do; check the Review screen.

## Prayer

**Pray Today.** Abide surfaces prayers on a cadence you choose (daily, weekly,
or as-led) so you're not scrolling a giant list. Mark a prayer **prayed** to
advance it, or **answered** when God answers.

**Requests.** Each prayer can hold updates over time, a category, and an
**anchor verse**. Mark a prayer **Ongoing** (a standing prayer that never
"completes"). You can **promote an anchor verse to a memorization card** in one
tap — pray it and learn it.

**Filters.** Status chips (All / Open / Ongoing / Answered / Archived) narrow
the list; Archived is the only way back to soft-archived prayers.

## Study

Open a passage and tap a verse to open its **Study panel**, which has four tabs:

- **Note** — your own study notes on that verse.
- **Cross-refs** — other verses that connect to this one (see below).
- **Commentary** — public-domain commentary (Matthew Henry; Jamieson-Fausset-Brown).
- **Original** — the Hebrew/Greek behind the verse (see the next section).

**About cross-references.** These are curated links between verses. Abide's come
from OpenBible.info, whose data traces back to the public-domain *Treasury of
Scripture Knowledge* plus crowd-sourced **voting**. That vote becomes a **rank**,
so Abide can show you the *strongest* connections first rather than a flat list.
Tap a cross-reference to preview the verse; tap again to collapse it.

## The Original (Hebrew/Greek) tab

The **Original** tab shows an **interlinear word list** — every Greek or Hebrew
word in the verse (Hebrew reads right-to-left), each with:

| Field | What it is | Why it matters |
|---|---|---|
| **Original word** | The actual Greek/Hebrew | What the author actually wrote |
| **Transliteration** | The word in English letters (e.g. *agapē*, *logos*) | Say it / recognize it without reading the script |
| **Gloss** | A short English meaning | Quick "what does it mean" |
| **Strong's number** | A stable ID (G#### = Greek, H#### = Hebrew) | Ties every occurrence of a word together |

Tap a word to open a lexicon sheet with the fuller definition **plus
parsing/morphology** (decoded readable — e.g. "verb, aorist, active").

### A beginner word-study workflow (no Greek required)

1. Read the verse in ESV or KJV first — get the plain sense.
2. Open **Original**; find the one or two words carrying the weight (usually the
   verb or a key noun — *love*, *word*, *righteousness*). Skip particles.
3. Tap that word; read the gloss and full entry; note the Strong's number.
4. Check the **parsing** — tense and mood matter (a Greek aorist "did, once" vs
   a present "keeps on doing" can change a command).
5. Compare occurrences — the same Strong's number elsewhere is the same word
   (cross-references often reuse the key term).

### The one trap: the word-study fallacy

Don't assume a word carries *all* its possible meanings — or its English-derived
meaning — in *every* verse. (The classic bad move: *dynamis* → "dynamite" →
"explosive power." It doesn't mean that.) **Context wins over the lexicon,
always.** Parsing and gloss *inform* the reading; they don't override the
sentence.

### Learn more

- **Blue Letter Bible** — free, same Strong's/interlinear model, beginner
  tutorials: <https://www.blueletterbible.org/help/interlinear.cfm>
- **STEP Bible** — the source of Abide's tagged data, so its UI mirrors ours:
  <https://www.stepbible.org>
- **Book:** *How to Read the Bible for All Its Worth* (Fee & Stuart) — the best
  layperson starting point; practical word-study section and the fallacies.
- **Advanced:** D. A. Carson, *Exegetical Fallacies*.

## Credits & attribution

- **Translations:** King James Version (public domain); English Standard Version
  (ESV®, © Crossway — used by permission). Full credits are in the app under
  Settings → Credits and Help → Translation Credits.
- **Commentary:** Matthew Henry and Jamieson-Fausset-Brown (public domain), via
  HelloAO.
- **Cross-references:** OpenBible.info (CC BY 4.0).
- **Original-language data:** STEPBible tagged text and lexicons.
