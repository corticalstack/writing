---
name: explainer-animation
description: >-
  Build a self-playing, scrubbable explainer animation as one dependency-free HTML file (inline SVG,
  CSS, vanilla JS). It walks through how a system, protocol, request flow or architecture works, in
  chapters, with plain-words captions, a timeline, keyboard controls and a pause-after-each-step mode
  for presenting. Use this whenever the user wants to animate or visualise a flow, sequence, handshake,
  token or data exchange, pipeline or architecture; asks for an "animated diagram", "motion graphic" or
  "explainer" for docs, a README, a lab or a talk; or is struggling to follow a multi-step process
  that a moving picture would make clear, even if they never say "animation". Not for 3D scenes or
  camera moves (use three.js), rendered video files, or a single static diagram.
license: MIT
---

# Explainer animation

Build an animated walk-through of how something works, as a single HTML file with no packages and no
build step. It opens from disk, plays by itself, and can be scrubbed, stepped and presented from.

The output is a 2D diagram that changes over time: actors appear, messages travel between them, checks
tick, and a caption says in plain words what is happening at each moment.

## When this is the wrong tool

- **3D scenes, camera moves, particles:** that needs WebGL. Use three.js.
- **A video file (MP4, GIF):** this makes a web page. Record it afterwards if a file is needed.
- **One picture with no sequence:** draw a static diagram.

## What you start from

| File | What it is | When to read it |
|---|---|---|
| `assets/template.html` | A complete, working animation (a cache-aside read) containing the engine, the player and a small demo | Copy it as your starting file, every time |
| `references/engine.md` | Every verb and building block, how timing works, recipes, and the mistakes that are easy to make | Before you write the script |

A full-size example with 18 chapters, built with this engine:
[source](https://github.com/corticalstack/awesome-foundry-nextgen/blob/main/docs/diagrams/08-05c-ticket-flow.html),
[play it](https://htmlpreview.github.io/?https://github.com/corticalstack/awesome-foundry-nextgen/blob/main/docs/diagrams/08-05c-ticket-flow.html).

Do not write the engine or the player from scratch. They took many rounds to get right (scrubbing,
reading-time holds, step pause), and the template's version is tested.

## Workflow

### 1. Get the facts from the source

Read the code, config or docs the animation explains, and list the facts it will state. Every caption
and every label is a claim. Check each one against the source before you draw it.

This matters more here than in prose. An animation states things with confidence, and the viewer cannot
ask what you meant. For example, "the gateway allows or refuses each call before it adds the API key"
can be read two ways: the call is checked before it is forwarded, or the response is filtered afterwards.
Only the policy file settles which, and the caption must then say it without the ambiguity.

If you cannot verify a claim, tell the user. Do not draw it as fact.

### 2. Agree the story

Put a short outline in your reply before you build: the title, the three overview points, and the
chapter titles grouped into parts. Then carry on, unless the scope is unclear. The outline costs one
message and lets the user redirect before the drawing exists.

| Piece | What it does | Guidance |
|---|---|---|
| Title | Names the problem | It must make sense to someone who has never heard of the project. "How user B's question reaches GNews" fails that test. "Per-user limits on an MCP server that knows no users" passes. Keep it to about 50 characters, so it fits on two lines |
| Overview | Says why this exists | A subtitle of up to about 20 words, then three points of up to about 35 words each: **The need**, **The gap**, **The fix**. Much longer and the block runs into the caption bar, which the audit reports |
| Set-up | Introduces the actors | Reveal each thing in the order it comes to exist, at the moment its caption appears. Start with the thing being consumed or protected, because it is the constraint everything else works around |
| Main path | One request, start to finish | One chapter per step |
| Second path | A failure, a denial or a repeat | This is what teaches the rule. The happy path alone does not show what the mechanism prevents |
| Summary | Who sees or does what | One short label per actor |

### 3. Lay out the stage

The stage is an SVG with a fixed 1600 x 840 coordinate space that scales to the window.

- Give each actor a panel. If one actor serves all the others (an identity provider, a control plane),
  put it across the top and run the request path left to right underneath.
- Make the gap between two panels at least as wide as the widest message that travels between them.
  Otherwise the message sits on top of both panels for its whole journey. The template's gap is 340,
  and its widest message, with its chip, is 340. Panels that exchange nothing need only 50.
- Keep 44 clear above the top row of panels. The summary's labels go there, each centred 24 above
  the panel it describes. A label centred on a panel's top edge lands on the panel's title unless
  the panel is very wide.
- Route connectors through the gaps. A line that crosses a title or a label looks like a mistake,
  because it is one.
- Use one colour per actor and one per thing that travels (a ticket, a key, a row). Keep the page
  chrome neutral. If the "Set-up" capsule is purple and so is the identity provider, viewers see a
  link that does not exist. Neutral means the part capsules, the caption bar and the controls. The
  title accent and the timeline bar are decoration and keep their colours.
- Design for a window of about 1600 x 900 or larger. At 1280 x 720 the stage scales to about half
  size. Captions and the title block still work there, but the diagram's text is too small to read.
- Show real names and values from the source in monospace: field names, scope names, paths.
- The stage carries names and values. The captions carry the explanation. Do not put sentences on
  the stage.

### 4. Write the script

Copy `assets/template.html`, then change every place marked `EDIT`. There are seven: the tab title,
the colours, the legend, the title block, the stage, the pieces added to the stage in code, and the
script. Keep the building-block functions and add your own beside them. Read `references/engine.md`
first.

- One chapter is one step. Its caption and its visible change happen at the same moment.
- Reveal one new thing at a time. If two boxes appear together, the viewer reads neither. The summary
  is the exception: its labels restate what was shown, so they may arrive together.
- Put exits (hiding a card, undoing a dim) at the chapter boundary, so they wait for the last caption.
- Something the viewer has to read off the stage, and that no caption repeats, needs at least 3.5
  seconds fully visible. A command flashed up for one second is not read. A message in transit can
  be quicker, because its caption says what it carries.
- For the second path, reset what the first path changed, then replay it.

### 5. Write captions the way a careful teacher talks

The user will read every caption and ask "what do you mean by that?" about any phrase that is vague.
Write so they do not need to.

| Instead of | Write | The rule |
|---|---|---|
| "Open the agent's front door" | "Let the news-desk app call the agent" | No metaphors in titles. Say the action |
| "Foundry talks to Entra ID for it" | "Foundry signs in to Entra ID as the agent and trades the user's ticket for one the gateway will accept" | Say who does what to whom |
| "Entra ID keeps the apps, consent and roles" | "Entra ID stores the apps, consent grants and role assignments" | Replace vague verbs (keeps, handles, manages, talks to) with the real one |
| "so it would refuse the original" | "user B's Ticket 1 is addressed to the agent, so the gateway would refuse it" | Name the thing. No "the original", "the former", "it" across sentences |
| "tokens whose `aud` is X" | "tokens whose `aud` (audience: the service a token is addressed to) is X" | Define a term in brackets the first time it appears |
| "Nothing else can make one." | (cut) | Drop the flourish. It raises a question you then have to answer |

Also:

- One idea per caption, three lines at most. When a caption grows, split it into two that follow each
  other. Do not shrink the font.
- Use one name for one thing everywhere. If the legend says "API key", no caption says "subscription key".
- Leave out references to where the content came from (lab step numbers, file names) unless asked.

### 6. Pace it

The engine holds the picture until each caption can be read: 3 words a second plus one second. You
set where things happen; it adds the time.

- A caption over about 40 words will sit for 15 seconds or more. Split it, or give it a faster pace
  with `cap(t, html, 4)`.
- You know the text, so you read it faster than a viewer will. Do not tune the timing to your own
  reading. When the user says a gap is too long or too short, change that one caption.
- The overview times itself from its word counts. Change its pace with `overview(selectors, {wps: 4})`.

### 7. Check it in a browser

Serve the folder (`python3 -m http.server 8765 --bind 127.0.0.1`, or VS Code Live Server) and open
`file.html?paused` with a browser-automation tool such as the Playwright MCP server.

1. **Audit.** At 1600 x 1000, run `anim.audit()` in the page. Fix until `problems` is empty. Repeat at
   1280 x 720. Text wraps differently at each size: a caption that takes three lines at one width can
   take four at the other. At the smaller size the audit adds a note that the stage text is too small
   to read. That is expected and is not a fault: the small size is for checking wrapping.
2. **Look.** For each entry in `anim.stops`, call `anim.seek(t)` and take a screenshot. A stop is the
   end of a chapter, when everything it adds is on screen, close-ups included. Then do the same with
   `anim.journeys`, which lists the middle of every journey: look at one or more per connector. Look
   for overlaps, connectors crossing labels, something hidden behind something else, and dimmed
   things that should be bright. The audit cannot see these.
3. **Console.** No errors or warnings. A warning that starts `no element for` is a mistyped selector.
4. **Play.** Call `anim.play()`, wait two seconds, and confirm `anim.time()` moved by about two.

The audit checks selectors, caption length and hold time, caption-bar height, text fitting inside
cards, close-up cards, messages and chips, the title block and the controls row. It does not replace
looking.

If you have no browser tool, say so. Ask the user to open the file and run `anim.audit()` in the
console, and do not report the animation as checked.

### 8. Hand over

- Save the file beside the document it explains, or in `docs/diagrams/`, and link to it from that
  document.
- Tell the user the running time, the chapter count and the controls.
- To share it:

| Situation | How |
|---|---|
| Editing, on the same machine | The local server address |
| Editing over a VS Code tunnel | Forward the server's port in the Ports view and keep it private |
| Merged to a public GitHub repo | `https://htmlpreview.github.io/?https://github.com/<owner>/<repo>/blob/<branch>/<path>` |
| A permanent address | GitHub Pages |

GitHub shows an HTML file as source code, which is why the preview link is needed. raw.githack.com
puts a click-through notice in front of the page, so avoid it.

## Working through the user's notes

Expect many small rounds. The user watches the animation and sends notes, often several at once.

- Make the change, run the audit again at both sizes, and look at the chapter you touched.
- Report what changed and the new running time.
- When a note shows that a phrase is vague, search for the same phrase elsewhere and offer to fix
  those too.
- When the user asks what something means, answer in plain words first. Then offer a clearer caption.
- When a change has a cost (a longer caption that needs a split, a title that cannot sit level with
  the top bar), say so and give the options. Do not quietly pick one.
