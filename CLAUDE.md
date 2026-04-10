# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo actually is

Despite the directory name `classic_interview_questions`, this is **not** a coding-interview question bank. It is the deliverable set for a short English speaking challenge (course: "English at Work") on the topic *"Ace Your Interview: Mastering 6 Classic Questions"*. The six questions are soft-skill HR questions (Tell me about yourself, Why this job, Greatest strength, Greatest weakness, A mistake you made, Your questions for us), not algorithmic problems.

There is no build system, no package manager, no tests, and no backend. Everything ships as static Markdown plus a single self-contained HTML file.

## Content pipeline

The four Markdown files and the HTML deck are **derivatives of one source**, and should stay in sync if any of them is edited:

1. [noi_dung_tham_khao.md](noi_dung_tham_khao.md) - original Vietnamese reference script (Ola). This is the *source of truth* for the ideas / framing. All downstream files are English adaptations of this.
2. [1_outline.md](1_outline.md) - condensed English outline (hook / 6 questions / close).
3. [2_full_speech.md](2_full_speech.md) - the full ~600-word speech (~4.5 min at 130 wpm). Includes bolded "signpost" lines the speaker pauses on, and a vocabulary table at the bottom marked *"do NOT read aloud"*.
4. [3_powerpoint_content.md](3_powerpoint_content.md) - slide-by-slide content spec for an 11-slide deck (Title → Reality Check → Roadmap → Q1–Q6 → Class Reflection → Thank You).
5. [presentation.html](presentation.html) - the rendered deck implementing the spec in (4).

If you change the speech content, the HTML slides and both Markdown specs usually need matching edits. The speech (2) has a strict word budget (~600 words / 4–5 min) - preserve it when editing.

## presentation.html - architecture notes

Single-file HTML: inline CSS + inline JS. External deps loaded from CDN: Google Fonts (Inter) and GSAP 3.12.5. No bundler, no build step - open the file in a browser. To preview a specific slide directly, append `?preview=N` where N is 1–11 (see the preview-mode block near the bottom of the `<script>`).

- **Slide structure**: 11 `<section class="slide" id="sN" data-i="N-1">` elements inside `#app`. Each slide is exactly `100vh`. IDs `s1`–`s11` map 1:1 to the 11 slides described in `3_powerpoint_content.md`.
- **Scroll locking**: the wheel handler calls `e.preventDefault()` and debounces input so each wheel gesture advances exactly one slide via `goTo(cur + dir)`. Touch swipe and arrow/PageUp/PageDown keys go through the same `goTo()` function. When adding slides, update the `TOTAL` reference and the `#counter` element, and make sure new sections keep sequential `data-i` values.
- **Animations**: every animated element carries `data-a="up|left|right|scale|fade"` and optionally `data-d="<seconds>"` for a delay. The `animateSlide()` function reads these attributes and runs a GSAP `fromTo` tween per element, once per slide (tracked in the `seen` Set). To add a new animated element, just add those `data-*` attributes - do not call GSAP directly.
- **Slide 2 has a special case**: the "47%" stat counter is hard-coded in `animateSlide()` keyed on `slide.id === 's2'` and targets `#stat-el`. If you renumber slides, update that check.
- **Intersection observer** at `threshold: 0.55` catches manual scrolls that bypass `goTo()` and keeps `cur` / the progress bar / nav dots / counter in sync.

## Editing conventions worth knowing

- The speech file uses **bolded quoted phrases** (e.g. `**"I want to say thank you"**`) as speaker cues / pause markers, not emphasis in the usual sense. Keep that convention if you edit.
- The vocabulary table at the bottom of [2_full_speech.md](2_full_speech.md) is reference-only for the speaker; it is explicitly marked not to be read aloud.
- The Vietnamese reference file is kept verbatim - do not translate it in place.
