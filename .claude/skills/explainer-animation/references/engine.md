# Engine reference

How `assets/template.html` works and how to script it.

## Contents

1. [The file's regions](#the-files-regions)
2. [The stage: coordinates and classes](#the-stage-coordinates-and-classes)
3. [Building blocks](#building-blocks)
4. [Verbs](#verbs)
5. [Chapters, parts and the overview](#chapters-parts-and-the-overview)
6. [How timing works](#how-timing-works)
7. [Recipes](#recipes)
8. [Checking: `anim` and the URL switches](#checking-anim-and-the-url-switches)
9. [Mistakes that are easy to make](#mistakes-that-are-easy-to-make)

## The file's regions

Seven places are marked `EDIT`. Change those and leave the rest.

| Region | Marked | Change it? |
|---|---|---|
| Tab title (`<title>`) | `EDIT` | Yes: the same words as the on-screen title |
| Colour variables in `:root` | `EDIT` | Yes: one colour per actor, one per travelling thing |
| Page chrome CSS (top bar, caption bar, controls) | | No |
| Stage vocabulary CSS (`.card`, `.row`, `.dot`, `.pill`, ...) | | Add classes; do not remove any |
| Legend (`.legend`) | `EDIT` | Yes: one entry per travelling thing, or delete it |
| Title block (`#title`) | `EDIT` | Yes |
| Stage (`<svg id="stage">`) and its `aria-label` | `EDIT` | Yes: replace all of it except `<defs>`, `#packets` and `#cards` |
| Building-block functions (`h`, `badge`, `chip`, `pill`, `detailCard`) | | Keep; add your own beside them |
| Pieces added to the stage in code | `EDIT` | Yes |
| ENGINE | | No, unless you add a verb |
| THE SCRIPT (`PARTS`, `overview(...)`, `chapter(...)`) | `EDIT` | Yes: replace all of it |
| READING TIME, PLAYER, SELF-CHECK | | No |

## The stage: coordinates and classes

The stage is `<svg viewBox="0 0 1600 840">`. Write every position in that space. The browser scales it.

| Class or attribute | On | Effect |
|---|---|---|
| `class="h"` | anything | Starts hidden. The script shows it |
| `class="draw"` | a `<path>` | Starts undrawn. `drawL` draws it in |
| `class="hop"` | a connector path | Grey line that gets an arrowhead once drawn |
| `style="--c:var(--actor)"` | a panel group | Sets the colour its children inherit |
| `class="panel-bg"` | a panel's `<rect>` | Tinted background and border in `--c` |
| `class="card"` with a child `rect.bg` | a group | A box inside a panel. `glow` brightens its border |
| `class="row"` with a child `rect.hl` | a group | A line of text with a highlight bar that `glow` turns on |
| `class="dot"` with `text.yes` and `text.no` | a group | An empty circle. Class `ok` makes it a green tick; `bad`, a red cross |
| `t-title`, `t-tag`, `t-h`, `t-b`, `t-m`, `mono` | `<text>` | Panel title, small caps tag, heading, body, muted, monospace |
| `data-x`, `data-y` | a block built in code | Where a block centred on (0, 0) sits when it is not travelling |

Keep these groups, in this order, at the end of the SVG: `#lines` (connectors and badges), the motion
paths, `#packets` (things that travel and pinned labels), `#cards` (close-up cards). Later groups draw
on top of earlier ones.

Author static content in absolute coordinates. Anything that travels along a path must be centred on
(0, 0).

The engine writes a CSS transform on every element that starts hidden (`.h`), every `.draw` path and
anything a verb touches. A CSS transform replaces a `transform` attribute, so do not put that
attribute on those elements. If a piece needs a fixed transform, wrap its content in an inner group
and put the attribute there.

Layout rules the audit cannot check:

- The gap between two panels is at least as wide as the widest message that travels between them,
  chip included. Otherwise the message covers both panels for its whole journey.
- Keep 44 clear above the top row of panels. The summary's pinned labels go there.
- A step badge stays clear of whatever travels on its connector: 32 above a horizontal connector, or
  beside a vertical one by half the traveller's width plus 24.
- Design for a window of about 1600 x 900. At 1280 x 720 the stage scales to about 0.56 and its
  text is too small to read.

## Building blocks

All return an SVG group. Append it to a group on the stage.

| Call | Makes | Notes |
|---|---|---|
| `h(tag, attrs, ...kids)` | Any SVG element | `attrs.text` sets the text content |
| `badge(n, x, y, id)` | A step number in a circle. Its id is `b<n>` unless you pass one | Starts hidden. Pass an id when the same number appears on a second connector, or two elements share an id and a verb moves both |
| `chip(label, color, {id, w})` | A small token: a ticket, a key, a row | Default width 92. With an `id` it starts hidden and can travel alone. A chip used as a pill's `tail` must not have an id |
| `pill(id, text, color, w, opts)` | A message in a rounded box | Starts hidden. `opts`: `mono`, `tail` (one chip attached on the right), `cls` (`bad` or `good`), `x` and `y` (pin it in place) |
| `detailCard(id, title, sub, color, rows, {w, nameW})` | A card for a close-up | Starts hidden, centred on (0, 0). Each row is `[name, value, note]`, with an optional `[.., tagText, tagColor]`. Row ids: `<id>-<name>`. Tag ids: `<id>-<name>-tag`. The default is 400 wide with a 72-wide name column, which fits names of about 8 characters. Widen both for longer names: `{w: 480, nameW: 130}` |

`w` is the width you give the box. Text is not measured, so allow about 7.5 per character at the
default size (8 for monospace) plus 30 of padding, and check the result in a screenshot.

## Verbs

Every verb takes a time first. Inside a chapter, write times as `a(n)`: n seconds into this chapter.
Selectors are CSS selectors and may match several elements.

| Verb | Does |
|---|---|
| `show(t, sel, dur = .6)` | Fades in |
| `hide(t, sel, dur = .5)` | Fades out |
| `pop(t, sel, dur = .7)` | Fades in with a small overshoot. Use it for boxes and badges |
| `set(t, sel, {x, y, o, s, d, k})` | Jumps properties at once. Use it to place a card before `pop` |
| `dim(t, sel, k, dur = .6)` | Dims to `k` (0 to 1) without changing what is shown or hidden. `dim(t, sel, 1)` restores |
| `glow(t, sel, until)` | Adds class `glow` from `t` to `until`. Works on `.card` and `.row` |
| `fx(t, sel, cls, on = true)` | Adds or removes any class at `t`. Use it for `.dot` states. A class written in the markup stays until the script turns it off |
| `drawL(t, sel, dur = .9)` | Draws a `.draw` path, then adds its arrowhead |
| `undraw(t, sel)` | Resets a drawn path so a later `drawL` can draw it again |
| `along(t, sel, pathSel, dur, {rev})` | Moves an element along a motion path. It does not change visibility |
| `trip(t, sel, pathSel, dur, {rev, keep})` | Shows an element, moves it along a path, then hides it. `keep: true` leaves it at the end |
| `tw(t, sel, to, dur, ease)` | The general form behind the verbs above. Eases: `lin`, `io`, `out`, `back` |
| `cap(t, html, wps)` | Sets the caption from `t`. `<b>` and `<code>` are styled. `wps` overrides the reading pace for this caption |

Properties: `o` opacity, `k` dimming multiplier, `x` and `y` position, `s` scale, `d` how much of a
`.draw` path is drawn (0 to 1). Opacity on screen is `o * k`.

## Chapters, parts and the overview

```js
const PARTS = ['Set-up', 'First read', 'Second read', 'Summary'];

overview(['#ti-kick', '#ti-1', '#ti-2', '#ti-3']);            // always first

chapter(1, 'Ask the cache', [1], 12, a => {                   // part index, title, step numbers, seconds
  drawL(a(.3), '#L1', .8); pop(a(.3), '#b1');
  cap(a(.3), 'The app asks the cache for the key <code>products:42</code>.');
  ...
});
```

- `PARTS` names the capsules in the top bar. A chapter's first argument is an index into it.
- The step numbers appear beside the chapter title. Match them to the badges on the stage.
- The seconds are the scripted length, before reading time is added.
- `overview(selectors, {wps, title, label})` builds the opening chapter. The title is on screen from
  the start. Each selector then appears in turn, once the one before it has had time to be read. The
  top bar stays hidden until the overview ends, so the title block can use the full width. The
  defaults are 3.5 words a second, the chapter title "The use case" and the capsule label "Overview".
- Chapters play in the order you write them. Each becomes a tick on the timeline and a stop for the
  Left and Right arrow keys and for "Pause after each step".
- The number keys 1 to 9 jump to a part. The footer hint does not list them.

## How timing works

You write the script in **script time**. The engine then turns it into **played time**.

1. Each caption owns the slot from its start to the next caption or the chapter's end, whichever
   comes first.
2. It needs `words / pace + 1` seconds. The default pace is 3 words a second.
3. If the slot is shorter, the engine inserts a pause at the **end of the slot** and shifts everything
   after it.

What follows from that:

- You never need to pad a chapter for reading. Write the times at which things happen and let the
  holds stretch it.
- Anything scheduled exactly at a chapter's end (`a(dur)`) or at the next chapter's start (`a(0)`)
  runs after the hold. That is where exits belong. An exit placed a second before the end runs before
  the hold, and the thing vanishes while its caption is still being read.
- A tween that spans the end of a slot is stretched by the hold. Keep movements inside one slot.
- A long caption makes a long pause. Split the caption, or give it a faster pace: `cap(t, html, 4)`.
- `anim.audit()` reports any caption that stays up for more than 20 seconds.

The picture is a pure function of the time. `render(t)` starts every element from its base state and
replays every change up to `t`. That is why scrubbing and jumping work, and it is why a verb must
never depend on what happened to be on screen before.

## Recipes

**Reveal a part with its caption**

```js
show(a(4), '#p-db', .8);
cap(a(4), 'The <b>database</b> holds the products table. It is the source of truth.');
```

**A message travels, and an answer comes back along the same path**

```js
drawL(a(.3), '#L1', .8); pop(a(.3), '#b1');
trip(a(.5), '#pk-get', '#mp1', 2);
trip(a(6), '#pk-miss', '#mp1', 1.8, {rev: true});
```

The connector (`#L1`) and the motion path (`#mp1`) are separate. The connector runs from panel edge to
panel edge. The motion path starts and ends inside the panels, so the message leaves from the sender
and lands on the receiver.

**A check that fills in**

```js
hide(a(4.5), '#chk-q', .2); show(a(4.6), '#chk-miss', .3); fx(a(4.6), '#chk', 'bad');
```

Put the question and each possible outcome at the same position as separate hidden texts, and swap them.

**Look closely at one thing**

```js
dim(a(8), ALL, .15); set(a(8), '#card-entry', {x: 800, y: 425}); pop(a(8.1), '#card-entry');
glow(a(15), '#card-entry-ttl', a(24)); pop(a(15), '#card-entry-ttl-tag');
hide(a(24), '#card-entry', .5); dim(a(24), ALL, 1);          // exits at the chapter boundary
```

`ALL` lists the panels, `#lines` and `#packets`. Do not include `#cards`, or the card dims too.

**Compare before and after**

Place two detail cards side by side with `set`, and give the rows of the second one tags such as
`SAME`, `NEW` and `ADDED`. Pop the tags one at a time, each with a caption about that row. The
full-size example does this in "What changed on the ticket".

**A second path through the same picture**

```js
hide(a(0), '#chk-miss,#db-ok', .3); fx(a(0), '#chk', 'bad', false); show(a(.4), '#chk-q', .3);
```

Reset at `a(0)` whatever the first path left behind, then replay the journey with the new outcome.

**Label each actor in the summary**

```js
pill('rc-cache', 'stores only what the app puts in it', 'var(--cache)', 300, {x: 1220, y: 26});   // the panel's top edge is at 50
...
pop(a(.3), '#rc-app'); pop(a(.9), '#rc-cache'); pop(a(1.5), '#rc-db');
```

Centre each label 24 above the top edge of its panel. Summary labels may arrive together under one
caption, because they restate what the animation already showed.

**Draw a connector that was drawn and hidden earlier**

```js
set(a(.5), '#L9', {o: 1}); undraw(a(.5), '#L9'); drawL(a(.5), '#L9', .6);
```

## Checking: `anim` and the URL switches

| Call | Returns or does |
|---|---|
| `anim.audit()` | `{viewport, duration, chapters, captions, maxCaptionLines, stageScale, problems, notes}`. `problems` is empty when every automatic check passes at this window size. `notes` are things to know that are not faults |
| `anim.stops` | `[{title, t}]`: one moment per chapter, just before it ends, when everything that chapter adds is on screen |
| `anim.journeys` | `[{what, t}]`: the middle of every `trip`, when the travelling thing is between panels |
| `anim.seek(t)` | Jumps to played time `t` |
| `anim.time()` | The current played time |
| `anim.chapters` | `[{part, title, nums, start, dur, label}]` in played time |
| `anim.caps` | `[{t, html, wps}]` in played time |
| `anim.duration` | Total played seconds |
| `anim.play()`, `anim.pause()` | Starts and stops playback |

| URL | Effect |
|---|---|
| `file.html?paused` | Does not auto-play. Use it for every check |
| `file.html#chapter-3` | Opens at chapter 3. The overview is chapter 0 |

What `audit()` checks:

- every selector the script used matched something
- no caption runs past three lines, and the caption bar keeps one height
- no caption stays up for more than 20 seconds
- no text spills out of its `.card`
- in a close-up card: no text spills out, the title clears the subtitle, each name clears its value,
  and no text runs under a tag
- each message's text fits its box, and each chip's label fits its chip
- the title block fits above the caption bar
- the controls fit on one row

It adds a note, not a problem, when the window is so small that the stage text renders under 9 px.

It does not see overlaps between separate elements, connectors crossing labels, or things hidden
behind other things. Look at screenshots for those.

To find the moment a caption starts: `anim.caps.find(c => c.html.includes('some words')).t`.

## Mistakes that are easy to make

| Mistake | What you see | Fix |
|---|---|---|
| A mistyped selector | Nothing happens at that moment. The console warns `no element for ...` | `anim.audit()` lists them |
| `marker-end` written on a `.draw` path | The arrowhead shows before the line draws | Use class `hop`, or add the marker in a `.done` rule as `.hop.done` does |
| An exit a second before the chapter ends | A card vanishes while its caption is still up | Put exits at `a(dur)` |
| A long label beside a pinned pill or badge | They overlap in one chapter only | Screenshot every chapter in which a pinned label is visible |
| A badge too close to its connector | A passing message covers it | 32 above a horizontal connector. Beside a vertical one by half the traveller's width plus 24 |
| A gap narrower than the message crossing it | The message covers both panels for the whole journey | Make the gap at least as wide as the widest message, chip included |
| A summary label centred on a panel's top edge | It lands on the panel's title | Centre it 24 above the panel, and keep 44 clear above the top row |
| The same step number on two connectors | Two badges share an id, and a verb moves both | Pass `badge` a fourth argument |
| A long name in a close-up card | The name runs into the value | `detailCard(..., {w, nameW})` |
| An id on a chip used as a pill's tail | The chip is hidden, and when shown it sits in the middle of the pill | Leave the id off |
| A connector straight across a panel title | It reads as a strike-through | Route it through the gap between panels, with rounded corners |
| `#cards` included in a `dim` | The close-up card dims with everything else | Dim the panels, `#lines` and `#packets` only |
| A badge that belongs to a close-up, placed in `#lines` | It dims with the lines | Append it to the panel that stays bright |
| An inline `transform` on something the engine moves | It jumps or will not move | Centre it on (0, 0) and use `data-x`, `data-y` or `set` |
| The title block built inside the SVG | It shrinks with the stage and leaves a wide margin | Keep it as the HTML overlay (`#title`) |
| A caption that fits at one window size only | Four lines at 1280 wide, three at 1600 | Run the audit at both sizes. Split the caption |
| Measuring captions by setting `#cap-text` yourself | The wrong caption stays on screen afterwards | Use `anim.audit()`, which restores it. Otherwise reload |
| Timing tuned to your own reading | The user says it is too fast | You know the text. Keep the default pace unless the user asks |
| Two new things appearing at once | The viewer misses one | Stagger them by 1.5 seconds or more, each with its own caption. A summary's labels are the exception |
| A command or value flashed up with no caption that repeats it | Nobody reads it | Keep it fully visible for 3.5 seconds or more |
