---
name: Report HTML
description: Render finished research as one self-contained HTML report in a fixed palette — dark lagoon header with an orange rule, numbered sections of one-sentence bullets, tables, inline-SVG diagrams, timelines and figures, in light and dark themes. Use when asked to write, build or export a report, write-up or summary as HTML, or to turn an analysis MD file into an HTML page.
---

# Report HTML

Turn research that is already done into one HTML page a reader can scan: short
bullets, tables and diagrams, the same palette in every report. Copy the
stylesheet in §5 verbatim. Never invent a color, font or component.

## 1. Check the inputs

| Situation | Action |
| --------- | ------ |
| Research and a list of questions or topics present | Go to §2 |
| An analysis MD file (for example `analysis-description` output) | Each `# N.` heading becomes section N; its bullets, tables and diagrams carry over |
| No questions, topics or MD file | Ask with the `question` tool for the list; never guess the outline |
| No research yet | Stop and say the research comes first; do not start it in this skill |
| Research misses a section | Keep its heading with one bullet `Not covered by the research: <what is missing>` |

Allowed: reopening a file or page the research already found, to pin an exact
link. Not allowed: new investigation.

Settle these before writing:

- **Title**: from the user; otherwise the workstream plus what the report
  answers.
- **Workstream**: the project or initiative. It goes in the eyebrow.
- **Date**: today, `YYYY-MM-DD`.

## 2. Plan the page

```text
header.hero
  .eyebrow       <Workstream> · Report · <YYYY-MM-DD>
  h1             <Topic>:<br><em>key word</em> and <em>key word</em>
  ul.toc         one pill per section: <b>01</b> <1–2 word name>
main
  section#s1     h2 "1. <Question>" → bullets → visuals → legend → source line
    h3#s1-1      "1.1. <Part>", only when the section has distinct parts
  section#s2     …
footer           Built from <input docs> · <YYYY-MM-DD>
```

- One section per question or topic, in the user's order and wording. Fix only
  obvious typos.
- Never merge, split, reorder, or add sections.
- `<title>`: 2–5 words, no date.
- `h1`: 1–3 key words in `<em>`, which render orange.
- `h3` parts: milestones, components, or options. Number them `N.M.`; a tag
  may follow the part title.

## 3. Palette

### 3.1 Tokens

All colors live in `:root` (§5). Dark values apply under
`prefers-color-scheme: dark` or `<html data-theme="dark">`. `—` means the
light value stays.

| Token | Light | Dark | Role |
| ----- | ----- | ---- | ---- |
| `--bg` | `#f5f5f4` | `#070707` | Page background |
| `--surface` | `#ffffff` | `#121212` | Panels: tables, cards, diagrams, callouts, timeline |
| `--surface-2` | `#fafaf9` | `#1b1b1b` | Table header and group rows, inline code, diagram nodes |
| `--ink` | `#0a0a0a` | `#eeeeee` | Headings, first bullet, first table column |
| `--ink-2` | `#404040` | `#cfcfcf` | Other bullets, table cells, card text |
| `--muted` | `#555555` | `#a3a3a3` | Sources, captions, labels, table header text, footer, secondary arrows |
| `--line` | `#e9e7dd` | `#242424` | Panel borders, row dividers |
| `--line-strong` | `#dedede` | `#333333` | Outlined tags, dashed group boxes, planned timeline track |
| `--orange` | `#ff6700` | — | Brand accent: header rule, `h2` number, bullet marker, callout bar, main path |
| `--orange-ink` | `#bf5000` | `#ff8a3d` | Orange text: links, `h3` number, card and callout labels |
| `--orange-soft` | `#fff1e7` | `#2a1608` | Accent tag and accent node fill, current-step ring |
| `--lagoon` | `#002828` | — | Header, dark card, key node, dark tag |
| `--lime` | `#cafe54` | — | Accents on lagoon only: eyebrow, TOC numbers, dark-card label, check marks |
| `--peach` | `#ffc98b` | — | Warning tag fill |
| `--emerald` | `#00654d` | `#3fd1a6` | Done and pass: tag, timeline dot |
| `--stone` | `#e9e9dc` | `#26261f` | Neutral tag fill |
| `--figure-bg` | `#ffffff` | — | Image frames; exported drawings stay on white |

Fixed values, the same in both themes, used only where listed:

| Value | Where |
| ----- | ----- |
| `#ffffff` | Header title, dark-card title, key-node text, current-step number, lightbox image backing |
| `#f4f4ed` | Header text, TOC pill text |
| `#d6dcd8` | Dark-card body text |
| `#ffffff08`, `#ffffff0a`, `#ffffff2a` | Header grid lines, TOC pill fill, TOC pill border |
| `#4a2600` | Warning tag text, on peach |
| `#0a0a0a` | Blocked tag text, on orange |
| `#000000d9` | Lightbox backdrop |

### 3.2 Colors per component

| Component | Element | Color |
| --------- | ------- | ----- |
| Page | Background, default text | `--bg`, `--ink` |
| Header `.hero` | Background | `--lagoon`, with a 32 px grid of `#ffffff08` lines |
| | Bottom rule | 4 px `--orange` |
| | Eyebrow | `--lime` |
| | Title `h1`, key words `<em>` | `#ffffff`, `--orange` |
| | TOC pill text, fill, border, hover border | `#f4f4ed`, `#ffffff0a`, `#ffffff2a`, `--lime` |
| | TOC number `<b>` | `--lime` |
| Section heading `h2` | Text, number | `--ink`, `--orange` |
| Part heading `h3` | Text, number, divider above | `--ink`, `--orange-ink`, `--line` |
| Sub-label `.sub-label` | Text | `--muted` |
| Bullets `ul.points` | First bullet, the answer | `--ink`, weight 500 |
| | Other bullets | `--ink-2` |
| | Marker | 7 px rounded square, `--orange` |
| Source `.src` | Text, link underline, link hover | `--muted`, dotted `--muted`, `--orange-ink` |
| Link in text | Text | `--orange-ink` |
| Inline `code` | Fill, border | `--surface-2`, `--line` |
| Table `.table-wrap` | Panel fill, border | `--surface`, `--line` |
| | Header row text, fill | `--muted`, `--surface-2` |
| | Cells, first column | `--ink-2`, `--ink` weight 600 |
| | Row divider | `--line` |
| | Group row `tr.group` text, fill | `--muted`, `--surface-2` |
| Card `.card` | Fill, border, label, title, text | `--surface`, `--line`, `--orange-ink`, `--ink`, `--ink-2` |
| Dark card `.card.dark` | Fill, label, title, text | `--lagoon`, `--lime`, `#ffffff`, `#d6dcd8` |
| Callout `.callout` | Fill, border, left bar, label | `--surface`, `--line`, 4 px `--orange`, `--orange-ink` |
| | Items, check mark | `--ink`, `--lagoon` ✓ on a `--lime` circle |
| Diagram `.diagram` | Panel fill, border | `--surface`, `--line` |
| | Node `.dg-node` fill, stroke, text | `--surface-2`, `--muted`, `--ink` |
| | Key node `.dg-key`: the system the report is about | `--lagoon` fill, `--lime` stroke, `#ffffff` text |
| | Accent node `.dg-accent`: the problem or the change | `--orange-soft` fill, `--orange` stroke, `--orange-ink` text |
| | Group box `.dg-group`, group label | dashed `--line-strong`, `--muted` |
| | Main path `.dg-edge.main` | `--orange`, 2 px |
| | Other edge `.dg-edge`, edge label `.dg-label` | `--muted` 1.5 px, `--muted` |
| | Async, planned or optional edge `.dashed` | Dashed, in its edge's color |
| Timeline `ol.timeline` | Panel | Same as the table panel |
| | Done step `.done` | `--emerald` dot, `--surface` ✓, solid `--orange` track |
| | Current step `.now` | `--orange` dot, `#ffffff` number, `--orange-soft` ring, solid `--orange` track |
| | Next step `.next` | `--lagoon` dot, `--lime` number and ring, dashed `--line-strong` track |
| | Later step `.later` | `--surface` dot with a dashed `--muted` border, `--muted` title |
| | Step label, title, note | `--muted` (`--emerald` done, `--orange-ink` current), `--ink`, `--muted` |
| Figure `figure` | Frame fill, border, caption, caption lead | `--figure-bg`, `--line`, `--muted`, `--ink` bold |
| Legend, source line | Text, link | `--muted`, dotted `--muted` |
| Footer | Text, top border | `--muted`, `--line` |

### 3.3 Tags

| Class | Look | Use for |
| ----- | ---- | ------- |
| `.t-accent` | `--orange-ink` on `--orange-soft`, `--orange` border | First or current: milestone 1, the chosen option |
| `.t-dark` | `--lime` on `--lagoon` | Second, or the target state |
| `.t-neutral` | `--ink-2` on `--stone` | In scope, not scheduled yet |
| `.t-later` | `--muted`, `--line-strong` border | Later, out of scope, todo |
| `.t-ok` | `--emerald` text and border | Done, merged, pass |
| `.t-warn` | `#4a2600` on `--peach` | In progress, in review, at risk, medium |
| `.t-bad` | `#0a0a0a` on `--orange` | Blocked, failing, high |
| `.tbd` | `--muted`, dashed border | Not decided yet |

Ticket and PR status: Backlog or Todo → `.t-later`, In Progress or In Review →
`.t-warn`, Done or merged → `.t-ok`, Blocked → `.t-bad`.

### 3.4 Rules

- Colors come only from the classes in §5: no `style` attributes, no new hex
  values, no new tokens.
- Orange marks the one thing per visual the reader should see first: the main
  path, the current step, the chosen option. Never decoration.
- Orange text uses `--orange-ink`. `--orange` is for fills, markers, lines,
  and the large `h2` number and `h1` key words.
- Lime only on lagoon; never lime on a light surface.
- Each visual that uses tags or colored lines gets a `.legend` under it.
- Fonts: Familjen Grotesk for `h1`–`h4`, card titles and step titles; Inter for
  body text; Geist Mono for the eyebrow, labels, tags, table headers, code and
  edge labels.

## 4. Write the content

- Bullets go in `<ul class="points">`, one `<li>` per bullet.
- The first bullet answers the section's question directly and renders bold.
  The rest support it.
- 2–5 bullets per section; 2–4 under an `h3` part.
- One short sentence per bullet: one line, at most ~20 words, no sub-bullets.
- Start with a noun or verb, never "This", "We", or "Basically".
- Numbers carry their base and time: `39 of 67 open clocks on 10-12`.
- Identifiers in `<code>`: files, fields, flags, tables, CLI options.
- No intro, summary, conclusion, or prose paragraphs. Card text, captions and
  callout items are one short sentence each.
- A sixth bullet is a sign the detail belongs in a table (§7).

## 5. Stylesheet and skeleton

Copy this file and replace only the `{…}` placeholders and the `<main>`
content. Keep the `<style>` block, the marker `<svg>`, the lightbox and the
script verbatim.

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>{2–5 word title}</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Familjen+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600&family=Geist+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  /* ---------- Palette ---------- */
  :root {
    --bg: #f5f5f4;
    --surface: #ffffff;
    --surface-2: #fafaf9;
    --ink: #0a0a0a;
    --ink-2: #404040;
    --muted: #555555;
    --line: #e9e7dd;
    --line-strong: #dedede;
    --orange: #ff6700;
    --orange-ink: #bf5000;
    --orange-soft: #fff1e7;
    --lagoon: #002828;
    --lime: #cafe54;
    --peach: #ffc98b;
    --emerald: #00654d;
    --stone: #e9e9dc;
    --figure-bg: #ffffff;
    --shadow: 0 1px 2px #0000000a, 0 4px 16px #0000000a;
  }
  @media (prefers-color-scheme: dark) {
    :root:not([data-theme="light"]) {
      --bg: #070707; --surface: #121212; --surface-2: #1b1b1b;
      --ink: #eeeeee; --ink-2: #cfcfcf; --muted: #a3a3a3;
      --line: #242424; --line-strong: #333333;
      --orange-ink: #ff8a3d; --orange-soft: #2a1608;
      --stone: #26261f; --emerald: #3fd1a6; --shadow: none;
    }
  }
  :root[data-theme="dark"] {
    --bg: #070707; --surface: #121212; --surface-2: #1b1b1b;
    --ink: #eeeeee; --ink-2: #cfcfcf; --muted: #a3a3a3;
    --line: #242424; --line-strong: #333333;
    --orange-ink: #ff8a3d; --orange-soft: #2a1608;
    --stone: #26261f; --emerald: #3fd1a6; --shadow: none;
  }

  /* ---------- Base ---------- */
  * { box-sizing: border-box; }
  html { -webkit-text-size-adjust: 100%; }
  body { margin: 0; background: var(--bg); color: var(--ink); font: 400 16px/1.6 Inter, system-ui, -apple-system, "Segoe UI", sans-serif; }
  a { color: var(--orange-ink); text-decoration: none; }
  a:hover { text-decoration: underline; }
  code { font: 500 0.86em "Geist Mono", ui-monospace, SFMono-Regular, Menlo, monospace; background: var(--surface-2); border: 1px solid var(--line); border-radius: 4px; padding: 0.05em 0.35em; }

  /* ---------- Header ---------- */
  .hero {
    background:
      linear-gradient(#ffffff08 1px, transparent 1px) 0 0 / 32px 32px,
      linear-gradient(90deg, #ffffff08 1px, transparent 1px) 0 0 / 32px 32px,
      var(--lagoon);
    color: #f4f4ed;
    border-bottom: 4px solid var(--orange);
  }
  .hero-inner { max-width: 1080px; margin: 0 auto; padding: 56px 24px 36px; }
  .eyebrow { font: 500 13px/1 "Geist Mono", ui-monospace, monospace; letter-spacing: 0.08em; text-transform: uppercase; color: var(--lime); }
  .hero h1 { font: 700 clamp(34px, 6vw, 56px)/1.05 "Familjen Grotesk", Inter, sans-serif; letter-spacing: -0.02em; margin: 14px 0 26px; color: #ffffff; }
  .hero h1 em { font-style: normal; color: var(--orange); }
  .toc { display: flex; flex-wrap: wrap; gap: 8px; margin: 0; padding: 0; list-style: none; }
  .toc a { display: inline-flex; align-items: center; gap: 8px; padding: 7px 14px; border: 1px solid #ffffff2a; border-radius: 999px; color: #f4f4ed; font-size: 14px; background: #ffffff0a; }
  .toc a:hover { border-color: var(--lime); text-decoration: none; }
  .toc b { font: 500 12px "Geist Mono", monospace; color: var(--lime); }

  /* ---------- Layout and headings ---------- */
  main { max-width: 1080px; margin: 0 auto; padding: 8px 24px 64px; }
  section { padding-top: 48px; scroll-margin-top: 16px; }
  h2 { font: 700 clamp(24px, 3.4vw, 32px)/1.2 "Familjen Grotesk", Inter, sans-serif; letter-spacing: -0.01em; margin: 0 0 18px; display: flex; gap: 12px; align-items: baseline; }
  h2 .num { color: var(--orange); }
  h3 { scroll-margin-top: 16px; font: 600 clamp(19px, 2.4vw, 22px)/1.3 "Familjen Grotesk", Inter, sans-serif; margin: 36px 0 14px; padding-top: 22px; border-top: 1px solid var(--line); display: flex; gap: 10px; align-items: baseline; flex-wrap: wrap; }
  h3 .num { color: var(--orange-ink); font-family: "Geist Mono", monospace; font-size: 0.8em; }
  .sub-label { font: 600 12px "Geist Mono", monospace; letter-spacing: 0.08em; text-transform: uppercase; color: var(--muted); margin: 26px 0 10px; }

  /* ---------- Bullets and sources ---------- */
  ul.points { margin: 0 0 20px; padding: 0; list-style: none; }
  ul.points > li { position: relative; padding: 6px 0 6px 22px; color: var(--ink-2); }
  ul.points > li::before { content: ""; position: absolute; left: 4px; top: 16px; width: 7px; height: 7px; border-radius: 2px; background: var(--orange); }
  ul.points > li:first-child { color: var(--ink); font-weight: 500; }
  .callout + ul.points > li:first-child { color: var(--ink-2); font-weight: 400; }
  .src { color: var(--muted); font-size: 0.86em; }
  .src a, .sourceline a, .legend a, footer a { color: var(--muted); border-bottom: 1px dotted currentColor; }
  .src a:hover, .sourceline a:hover, .legend a:hover, footer a:hover { color: var(--orange-ink); text-decoration: none; }
  .sourceline { font-size: 13px; color: var(--muted); margin: 8px 2px 0; }

  /* ---------- Cards ---------- */
  .cards { display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: 16px; margin: 4px 0 8px; }
  .card { background: var(--surface); border: 1px solid var(--line); border-radius: 14px; padding: 20px 22px; box-shadow: var(--shadow); }
  .card .k { font: 500 12px "Geist Mono", monospace; letter-spacing: 0.08em; text-transform: uppercase; color: var(--orange-ink); }
  .card h4 { font: 700 22px/1.2 "Familjen Grotesk", sans-serif; margin: 6px 0 8px; color: var(--ink); }
  .card p { margin: 0; color: var(--ink-2); }
  .card.dark { background: var(--lagoon); border-color: var(--lagoon); }
  .card.dark .k { color: var(--lime); }
  .card.dark h4 { color: #ffffff; }
  .card.dark p { color: #d6dcd8; }

  /* ---------- Callout ---------- */
  .callout { border: 1px solid var(--line); border-left: 4px solid var(--orange); background: var(--surface); border-radius: 12px; padding: 16px 20px; margin: 4px 0 18px; box-shadow: var(--shadow); }
  .callout .k { font: 600 12px "Geist Mono", monospace; letter-spacing: 0.08em; text-transform: uppercase; color: var(--orange-ink); }
  .callout ul { list-style: none; margin: 8px 0 0; padding: 0; }
  .callout li { position: relative; padding: 4px 0 4px 28px; font-weight: 500; color: var(--ink); }
  .callout li::before { content: "✓"; position: absolute; left: 0; top: 7px; width: 19px; height: 19px; border-radius: 50%; background: var(--lime); color: var(--lagoon); font: 700 12px/19px Inter, sans-serif; text-align: center; }

  /* ---------- Tables ---------- */
  .table-wrap { overflow-x: auto; background: var(--surface); border: 1px solid var(--line); border-radius: 12px; box-shadow: var(--shadow); margin: 4px 0 8px; }
  table { border-collapse: collapse; width: 100%; font-size: 14px; line-height: 1.5; }
  th, td { text-align: left; vertical-align: top; padding: 11px 14px; border-bottom: 1px solid var(--line); }
  thead th { font: 600 12px "Geist Mono", monospace; letter-spacing: 0.05em; text-transform: uppercase; color: var(--muted); background: var(--surface-2); white-space: nowrap; }
  tbody tr:last-child td { border-bottom: 0; }
  td { color: var(--ink-2); }
  td:first-child { font-weight: 600; color: var(--ink); }
  table.mid { min-width: 720px; }
  table.wide { min-width: 820px; }
  tr.group td { background: var(--surface-2); font: 600 12px "Geist Mono", monospace; letter-spacing: 0.05em; text-transform: uppercase; color: var(--muted); padding: 8px 14px; }

  /* ---------- Tags and legend ---------- */
  .tag { display: inline-block; font: 600 11px/1 "Geist Mono", monospace; letter-spacing: 0.04em; padding: 5px 8px; border-radius: 6px; white-space: nowrap; border: 1px solid transparent; }
  .t-accent { background: var(--orange-soft); color: var(--orange-ink); border-color: var(--orange); }
  .t-dark { background: var(--lagoon); color: var(--lime); }
  .t-neutral { background: var(--stone); color: var(--ink-2); }
  .t-later { color: var(--muted); border-color: var(--line-strong); }
  .t-ok { color: var(--emerald); border-color: var(--emerald); }
  .t-warn { background: var(--peach); color: #4a2600; }
  .t-bad { background: var(--orange); color: #0a0a0a; }
  .tbd { display: inline-block; padding: 4px 10px; border-radius: 6px; border: 1px dashed var(--muted); color: var(--muted); font: 600 12px/1 "Geist Mono", monospace; letter-spacing: 0.06em; }
  .legend { display: flex; flex-wrap: wrap; gap: 14px; font-size: 13px; color: var(--muted); margin: 10px 2px 0; }
  .legend > span { display: inline-flex; gap: 6px; align-items: center; }

  /* ---------- Diagrams (inline SVG) ---------- */
  .dg-defs { position: absolute; }
  .diagram { background: var(--surface); border: 1px solid var(--line); border-radius: 14px; padding: 18px; margin: 18px 0 8px; box-shadow: var(--shadow); overflow-x: auto; }
  .diagram svg { display: block; width: 100%; min-width: 640px; height: auto; }
  .diagram text { font: 500 14px Inter, sans-serif; fill: var(--ink); text-anchor: middle; dominant-baseline: central; }
  .diagram text.dg-label { font: 500 11px "Geist Mono", monospace; fill: var(--muted); }
  .diagram text.dg-group-label { font: 600 11px "Geist Mono", monospace; letter-spacing: 0.06em; fill: var(--muted); text-anchor: start; }
  .diagram text.dg-on-key { fill: #ffffff; }
  .diagram text.dg-on-accent { fill: var(--orange-ink); }
  .dg-node { fill: var(--surface-2); stroke: var(--muted); stroke-width: 1.2; }
  .dg-key { fill: var(--lagoon); stroke: var(--lime); stroke-width: 2; }
  .dg-accent { fill: var(--orange-soft); stroke: var(--orange); stroke-width: 1.5; }
  .dg-group { fill: none; stroke: var(--line-strong); stroke-width: 1.5; stroke-dasharray: 6 5; }
  .dg-edge { fill: none; stroke: var(--muted); stroke-width: 1.5; }
  .dg-edge.main { stroke: var(--orange); stroke-width: 2; }
  .dg-edge.dashed { stroke-dasharray: 5 5; }
  .dg-head { fill: var(--muted); }
  .dg-head.main { fill: var(--orange); }

  /* ---------- Figures (images) ---------- */
  figure { margin: 18px 0 8px; }
  .frame { display: block; width: 100%; background: var(--figure-bg); border: 1px solid var(--line); border-radius: 14px; padding: 14px; cursor: zoom-in; box-shadow: var(--shadow); }
  .frame img { display: block; width: 100%; height: auto; }
  figcaption { font-size: 14px; color: var(--muted); margin: 10px 2px 0; }
  figcaption b { color: var(--ink); font-weight: 600; }
  .lightbox { position: fixed; inset: 0; z-index: 10; background: #000000d9; display: none; align-items: center; justify-content: center; padding: 16px; cursor: zoom-out; }
  .lightbox.open { display: flex; }
  .lightbox img { max-width: 100%; max-height: 100%; background: #ffffff; border-radius: 10px; padding: 10px; }

  /* ---------- Timeline ---------- */
  .timeline { list-style: none; margin: 4px 0 8px; padding: 26px 24px 22px; display: grid; grid-auto-flow: column; grid-auto-columns: 1fr; gap: 20px; background: var(--surface); border: 1px solid var(--line); border-radius: 14px; box-shadow: var(--shadow); }
  .tl-item { position: relative; padding-top: 38px; min-width: 0; }
  .tl-item::before { content: ""; position: absolute; top: 10px; left: 24px; right: -20px; height: 3px; border-radius: 2px; background: repeating-linear-gradient(90deg, var(--line-strong) 0 8px, transparent 8px 14px); }
  .tl-item.done::before, .tl-item.now::before { background: var(--orange); }
  .tl-item:last-child::before { right: 8px; }
  .tl-item:last-child::after { content: ""; position: absolute; top: 6px; right: 0; border: 5px solid transparent; border-left: 8px solid var(--line-strong); border-right: 0; height: 1px; }
  .tl-dot { position: absolute; top: 0; left: 0; z-index: 1; width: 23px; height: 23px; border-radius: 50%; display: grid; place-items: center; font: 700 11px/1 "Geist Mono", monospace; }
  .done .tl-dot { background: var(--emerald); color: var(--surface); }
  .now .tl-dot { background: var(--orange); color: #ffffff; box-shadow: 0 0 0 4px var(--orange-soft); }
  .next .tl-dot { background: var(--lagoon); color: var(--lime); box-shadow: 0 0 0 4px var(--lime); }
  .later .tl-dot { background: var(--surface); color: var(--muted); border: 2px dashed var(--muted); }
  .tl-k { font: 600 11px "Geist Mono", monospace; letter-spacing: 0.08em; text-transform: uppercase; color: var(--muted); }
  .now .tl-k { color: var(--orange-ink); }
  .done .tl-k { color: var(--emerald); }
  .tl-item h4 { font: 700 18px/1.3 "Familjen Grotesk", Inter, sans-serif; margin: 4px 0 8px; color: var(--ink); }
  .later h4 { color: var(--muted); }
  .tl-note { font-size: 14px; color: var(--muted); }

  /* ---------- Spacing between stacked visuals ---------- */
  :is(.cards, .table-wrap, .diagram, figure, .timeline, .legend, .sourceline) + :is(.cards, .table-wrap, .diagram, figure, .timeline) { margin-top: 22px; }

  /* ---------- Footer ---------- */
  footer { max-width: 1080px; margin: 0 auto; padding: 24px 24px 48px; border-top: 1px solid var(--line); font-size: 13px; color: var(--muted); }

  /* ---------- Phone ---------- */
  @media (max-width: 640px) {
    .hero-inner { padding: 40px 16px 28px; }
    main { padding: 0 16px 48px; }
    footer { padding: 20px 16px 40px; }
    th, td { padding: 10px 12px; }
    .frame { padding: 8px; border-radius: 10px; }
    .diagram { padding: 10px; }
    .timeline { grid-auto-flow: row; gap: 0; padding: 20px 16px 6px; }
    .tl-item { padding: 0 0 22px 40px; }
    .tl-item::before { top: 26px; bottom: -2px; left: 10px; right: auto; width: 3px; height: auto; background: repeating-linear-gradient(180deg, var(--line-strong) 0 8px, transparent 8px 14px); }
    .tl-item:last-child::before, .tl-item:last-child::after { display: none; }
    .tl-item h4 { margin-top: 0; }
  }
</style>
</head>
<body>
<svg class="dg-defs" width="0" height="0" aria-hidden="true">
  <defs>
    <marker id="ah" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path class="dg-head" d="M0,0 L10,5 L0,10 z"/></marker>
    <marker id="ah-main" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse"><path class="dg-head main" d="M0,0 L10,5 L0,10 z"/></marker>
  </defs>
</svg>

<header class="hero">
  <div class="hero-inner">
    <div class="eyebrow">{Workstream} · Report · {YYYY-MM-DD}</div>
    <h1>{Topic}:<br><em>{key word}</em> and <em>{key word}</em></h1>
    <ul class="toc">
      <li><a href="#s1"><b>01</b> {Name}</a></li>
      <li><a href="#s2"><b>02</b> {Name}</a></li>
    </ul>
  </div>
</header>

<main>
<section id="s1">
  <h2><span class="num">1.</span> {Question 1}</h2>
  <ul class="points">
    <li>{Answer} <span class="src">(<a href="{url}">{source}</a>)</span></li>
    <li>{Supporting fact} <span class="src">(<a href="{url}">{source}</a>)</span></li>
  </ul>
  {visuals, §6}
</section>
</main>

<footer>
  Built from <a href="{path}">{input doc}</a> and <a href="{path}">{input doc}</a> · {YYYY-MM-DD}
</footer>

<div class="lightbox" id="lightbox" role="dialog" aria-label="Enlarged figure"><img alt=""></div>
<script>
  (function () {
    var box = document.getElementById("lightbox");
    var big = box.querySelector("img");
    document.querySelectorAll("[data-zoom]").forEach(function (link) {
      link.addEventListener("click", function (e) {
        e.preventDefault();
        var img = link.querySelector("img");
        big.src = img.src;
        big.alt = img.alt;
        box.classList.add("open");
      });
    });
    box.addEventListener("click", function () { box.classList.remove("open"); });
    document.addEventListener("keydown", function (e) { if (e.key === "Escape") box.classList.remove("open"); });
  })();
</script>
</body>
</html>
```

## 6. Components

Every visual sits right after the bullets it explains. A visual built from
facts ends with a legend when it uses tags or colored lines, then a source
line.

**Table**: `.mid` for 3–4 columns, `.wide` for 4+ columns with long cells.
Both scroll inside the panel on a phone. Group rows split a long table.

```html
<div class="table-wrap">
  <table class="mid">
    <thead><tr><th>{Item}</th><th>Today</th><th>Success</th><th>Status</th></tr></thead>
    <tbody>
      <tr class="group"><td colspan="4">{Group name}</td></tr>
      <tr><td>{Item}</td><td>{cell} <span class="src">(<a href="{url}">{source}</a>)</span></td><td>{cell}</td><td><span class="tag t-warn">In progress</span></td></tr>
    </tbody>
  </table>
</div>
<div class="legend">
  <span><span class="tag t-ok">Done</span> Merged</span>
  <span><span class="tag t-warn">In progress</span> In review</span>
</div>
<p class="sourceline">Source: <a href="{url}">{source}</a></p>
```

**Cards**: 2–3 outputs or options side by side. One `.card.dark` for the main
or chosen one.

```html
<div class="cards">
  <div class="card dark"><div class="k">Option 1</div><h4>{Name}</h4><p>{One sentence}</p></div>
  <div class="card"><div class="k">Option 2</div><h4>{Name}</h4><p>{One sentence}</p></div>
</div>
```

**Callout**: success criteria, goals, or decisions, 2–4 items. It goes before
the part's bullets.

```html
<div class="callout">
  <div class="k">Success looks like</div>
  <ul><li>{Outcome}</li><li>{Outcome}</li></ul>
</div>
```

**Timeline**: milestones, phases, or rollout steps, 3–6 items. Exactly one
`.now`; `.done` items come before it, `.next` and `.later` after.

```html
<ol class="timeline" aria-label="{Name} timeline">
  <li class="tl-item done"><span class="tl-dot">✓</span><div class="tl-k">Done</div><h4>{Step}</h4><div class="tl-note">{repo#n}</div></li>
  <li class="tl-item now"><span class="tl-dot">2</span><div class="tl-k">Now</div><h4>{Step}</h4><div class="tl-note"><a href="#s3-1">Details in 3.1</a></div></li>
  <li class="tl-item next"><span class="tl-dot">3</span><div class="tl-k">Next</div><h4>{Step}</h4></li>
  <li class="tl-item later"><span class="tl-dot">4</span><div class="tl-k">Later</div><h4>{Step}</h4><div class="tl-note"><span class="tbd">TBD</span></div></li>
</ol>
```

**Part heading**: an `h3` with an optional tag.

```html
<h3 id="s3-1"><span class="num">3.1.</span> {Part title} <span class="tag t-accent">Milestone 1</span></h3>
```

**Sub-label**: a small heading above a visual inside a section, for example
`This system covers three use cases`.

```html
<p class="sub-label">{Label}</p>
```

**Diagram**: inline SVG (§7). **Figure**: an existing image (§7).

## 7. Add visuals

Diagrams and tables are encouraged. Pick by what the idea is:

| Idea | Visual | Component |
| ---- | ------ | --------- |
| Options, before/after, today vs. success, values per item | Table | `.table-wrap` |
| Request flow, architecture, pipeline, dependencies | Flow diagram | `.diagram` SVG |
| Call order between services | Sequence diagram | `.diagram` SVG with lifelines |
| Milestones, phases, rollout steps | Timeline | `ol.timeline` |
| 2–3 outputs, options or outcomes side by side | Cards | `.cards` |
| Success criteria, goals, decisions | Callout | `.callout` |
| A drawing that already exists (Excalidraw, screenshot) | Figure | `figure` with an embedded PNG |

- Every section with more than one fact gets at least one visual. Prefer a
  table or diagram over a sixth bullet.
- At most 3 visuals per section.
- Every node, row, cell and step comes from the research, labeled with real
  names (services, files, fields, tickets).
- Size: about 10 nodes per diagram, 12 rows per table (group rows extra), 6
  steps per timeline. Bigger → split or drop detail.

### Diagrams in SVG

- Style only with the `.dg-*` classes. No `fill`, `stroke` or `style`
  attributes, so the diagram follows the theme.
- No Mermaid or other JS libraries. The page must render offline, in both
  themes.
- `viewBox` 960 wide, height as needed. Nodes 150–180 × 48, `rx="10"`, at
  least 40 px apart. Center text at `x + w/2`, `y + h/2`.
- Flow left to right. A second row holds returns or async paths.
- Arrows end 6 px before the target box. `marker-end="url(#ah-main)"` on
  `.dg-edge.main`, `url(#ah)` on the others. The markers live once in the
  skeleton.
- Edge labels: at most 3 words, in `text.dg-label`, 14 px above the edge.
- One `.dg-key` node, the system the report is about. At most one
  `.dg-accent` node, the problem or the change.
- Group related nodes in a `.dg-group` rect with a `text.dg-group-label` in
  its top-left corner, uppercase.
- Sequence diagram: participants as nodes along the top, a dashed `.dg-edge`
  lifeline down from each, messages as numbered horizontal edges from top to
  bottom.
- `role="img"` and an `aria-label` that says the flow in one sentence.

```html
<div class="diagram">
  <svg viewBox="0 0 960 200" role="img" aria-label="{One sentence: what flows where}">
    <rect class="dg-group" x="230" y="20" width="250" height="160" rx="12"/>
    <text class="dg-group-label" x="246" y="40">{GROUP}</text>
    <rect class="dg-node" x="20" y="76" width="170" height="48" rx="10"/>
    <text x="105" y="100">{Caller}</text>
    <rect class="dg-accent" x="270" y="76" width="170" height="48" rx="10"/>
    <text class="dg-on-accent" x="355" y="100">{The change}</text>
    <rect class="dg-key" x="560" y="76" width="170" height="48" rx="10"/>
    <text class="dg-on-key" x="645" y="100">{The system}</text>
    <path class="dg-edge main" d="M190,100 H264" marker-end="url(#ah-main)"/>
    <text class="dg-label" x="227" y="86">{label}</text>
    <path class="dg-edge main" d="M440,100 H554" marker-end="url(#ah-main)"/>
    <text class="dg-label" x="497" y="86">{label}</text>
  </svg>
</div>
<div class="legend">
  <span><svg width="26" height="8"><path class="dg-edge main" d="M0,4 H26"/></svg> {Main path}</span>
  <span><svg width="26" height="8"><path class="dg-edge dashed" d="M0,4 H26"/></svg> {Async}</span>
</div>
<p class="sourceline">Source: <a href="{url}">{source}</a></p>
```

### Figures

- Use an existing drawing as is. Export Excalidraw as PNG at 2×, on a white
  background.
- Caption: `<b>Figure N · {Name}.</b>` plus one sentence on what to look at.
- `alt` says what the image shows, in one or two sentences.
- Embed every image as base64 so the report is one file: write
  `src="{file}.png"` with the PNG next to the report, then run:

```sh
python3 -I - <report.html> <<'EOF'
import base64, pathlib, re, sys
page = pathlib.Path(sys.argv[1])
def embed(m):
    data = base64.b64encode((page.parent / m.group(1)).read_bytes()).decode()
    return f'src="data:image/png;base64,{data}"'
page.write_text(re.sub(r'src="([^"]+\.png)"', embed, page.read_text()))
EOF
```

```html
<figure>
  <a class="frame" href="#" data-zoom><img src="{file}.png" alt="{What the image shows}"></a>
  <figcaption><b>Figure 1 · {Name}.</b> {What to look at}. Click to enlarge.</figcaption>
</figure>
```

## 8. Cite sources

Citations follow `analysis-description` §6, MD file mode: links, with
permalinks pinned to a full commit SHA. In HTML:

| Where | Form |
| ----- | ---- |
| Bullet | `<span class="src">(<a href="{url}">{source}</a>)</span>` at the end |
| Table cell | The same span, inside the cell |
| Visual | `<p class="sourceline">Source: …</p>` under it, after the legend; caveats go here too |
| Footer | Every input doc, plus the date |

- A bullet that is your inference or recommendation carries no source.
- Local docs: a relative path from the report's folder, spaces as `%20`.
- At most two sources per bullet. Never invent a citation.

## 9. Check

Run from a scratch folder (the session scratchpad when there is one). Set `R`
to the report's absolute path.

```sh
R=/abs/path/to/report.html
sed '/<style>/,/<\/style>/d' "$R" | grep -nE 'style=|#[0-9a-fA-F]{6}\b' || echo "colors: ok"

CH="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"
shot() { "$CH" --headless=new --hide-scrollbars --virtual-time-budget=4000 --window-size="$1" --screenshot="$PWD/$2.png" "file://$3" >/dev/null 2>&1; }
sed 's/<html lang="en">/<html lang="en" data-theme="dark">/' "$R" > dark.html
printf '<!doctype html><body style="margin:0"><iframe src="file://%s" width="390" height="6000" style="border:0"></iframe>' "$R" > phone.html
shot 1280,4000 light "$R"
shot 1280,4000 dark "$PWD/dark.html"
shot 600,6000 phone "$PWD/phone.html"
```

- `colors: ok` is the only output of the grep. Anything else is a color or
  style outside the stylesheet; replace it with a class.
- Raise the screenshot heights until the footer shows.
- The phone shot uses a 390 px iframe because headless Chrome will not make a
  window that narrow.
- Open all three screenshots and look at them. Then check:
  - No text runs off the right edge on the phone; only tables and diagrams
    scroll, inside their panels.
  - Dark theme: all text readable; the only white panels are image frames.
  - Each section's first bullet answers its question.
  - Each tag and colored line is explained in a legend.
  - Diagram labels do not overlap nodes, edges or each other.
- Fix and re-run until all pass.

## 10. Deliver

| Case | Path |
| ---- | ---- |
| User names a path | That path |
| Otherwise | `reports/<mmdd>-<topic>-report.html` next to the research docs; ask when unclear |

- Reply with one line: the file path.
- Publish or share the report only when the user asks.

## Example

Questions:

```text
(1) Why do checkout requests time out at peak traffic?
(2) Which setting should change first?
```

Section 1 of the report. The rest of the page is the §5 skeleton.

```html
<section id="s1">
  <h2><span class="num">1.</span> Why do checkout requests time out at peak traffic?</h2>
  <ul class="points">
    <li>Payment calls queue for a connection because the HTTP pool caps at 10 <span class="src">(<a href="https://github.com/acme/orders-api/blob/4f2c9e1b7a0d3c5e8f6a2b1c9d0e7f3a5b8c4d2e/orders/payments/client.py#L31">client.py#L31</a>)</span></li>
    <li>Peak traffic keeps about 40 payment calls in flight <span class="src">(<a href="https://grafana.example.com/d/payments">Grafana "Payments in-flight"</a>, 10-28 14:00–15:00 UTC)</span></li>
    <li>The 5 s client timeout includes queue time, so queued calls expire early <span class="src">(<a href="https://github.com/acme/orders-api/blob/4f2c9e1b7a0d3c5e8f6a2b1c9d0e7f3a5b8c4d2e/orders/payments/client.py#L34">client.py#L34</a>)</span></li>
  </ul>
  <div class="diagram">
    <svg viewBox="0 0 960 200" role="img" aria-label="checkout acquires a connection from the HTTP pool of 10, which posts charges to the payment provider; 30 calls wait in the pool">
      <rect class="dg-group" x="230" y="20" width="250" height="160" rx="12"/>
      <text class="dg-group-label" x="246" y="40">ORDERS-API</text>
      <rect class="dg-node" x="20" y="76" width="170" height="48" rx="10"/>
      <text x="105" y="100">checkout</text>
      <rect class="dg-accent" x="270" y="76" width="170" height="48" rx="10"/>
      <text class="dg-on-accent" x="355" y="100">HTTP pool (max 10)</text>
      <rect class="dg-key" x="560" y="76" width="170" height="48" rx="10"/>
      <text class="dg-on-key" x="645" y="100">Payment provider</text>
      <path class="dg-edge main" d="M190,100 H264" marker-end="url(#ah-main)"/>
      <text class="dg-label" x="227" y="86">acquire</text>
      <path class="dg-edge main" d="M440,100 H554" marker-end="url(#ah-main)"/>
      <text class="dg-label" x="497" y="86">POST /charges</text>
      <text class="dg-label" x="355" y="150">30 calls wait here</text>
    </svg>
  </div>
  <div class="legend">
    <span><svg width="26" height="8"><path class="dg-edge main" d="M0,4 H26"/></svg> Request path</span>
  </div>
  <p class="sourceline">Source: <a href="https://github.com/acme/orders-api/blob/4f2c9e1b7a0d3c5e8f6a2b1c9d0e7f3a5b8c4d2e/orders/payments/client.py#L31-L34">client.py#L31-L34</a></p>
</section>
```

Section 2 follows the same pattern: two bullets, then a settings table whose
`Status` column tags the setting to change first:

```html
<tr><td><code>max_connections</code></td><td>10</td><td>50</td><td><span class="tag t-accent">First</span></td></tr>
```
