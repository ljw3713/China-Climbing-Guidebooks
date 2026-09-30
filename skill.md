---
name: climbing-guidebook-pdf
description: Create a compact, print-ready climbing guidebook PDF from an offline theCrag HTML save or ZIP, preserving phototopo line geometry and verified route metadata. Use for route-guide or topo-guidebook requests; not for a simple route-list export.
metadata:
  short-description: Build a verified climbing topo PDF
---

# Climbing Guidebook PDF

Create a field-useful, A4-printable and phone-readable climbing guidebook from a locally saved theCrag page. Treat the local page as the source of truth: do not supplement route facts, names, setters, dates, or topo lines from memory or web search unless the user explicitly provides another authoritative source.

## Inputs and scope

Accept an HTML file directly or a ZIP that contains the saved page and its `_files` directory. Inspect the extracted structure before authoring.

Ask only for decisions that materially affect the result, such as whether the user wants a full guidebook, a single crag section, or alternate table fields. If no layout is specified, make a compact A4 portrait guidebook with a cover, a numbered quick index, and spatial topo pages.

Use the source's original route numbers everywhere. If its numbering contains gaps, report and preserve the gaps; never renumber records to make a continuous sequence.

## Source parsing

Parse route records from `div.route[data-nid]` and its `data-route-tick` JSON. For each actual route, extract only fields the user requests. Typical fields are:

- original number from `.num.toponum`
- name, grade, stars, type/style, height, and bolts from `data-route-tick`
- setter and setting date only from a route-history item whose label is exactly `Set:`

`FA:` is not a substitute for `Set:`. When either the setter or the setting date is absent, print `—`; never infer it from a first ascent, a neighboring route, a comment, a URL, or a date elsewhere on the page.

The saved HTML may contain irrelevant warnings, descriptions, activity controls, comments, popularity, and repeated route references. Exclude them unless the user specifically asks to retain them.

Before PDF generation, emit a small audit: count actual route records, list original numbers, identify number gaps, count routes with `Set:` metadata, and identify routes without usable topo geometry.

## Topo recovery and redrawing

Each `.phototopo[data-topodata]` contains structured JSON entries and a linked local image. Use those structured entries, not OCR, to recover the route number, route identity, and polyline coordinates.

Draw only the route lines assigned to the current topo page. Use a high-contrast but narrow yellow line so adjacent routes remain distinguishable; choose the thinnest practical stroke after rendering at target PDF size. Map `points` to the full image using the saved view dimensions and `data-view-scale`; do not assume the displayed HTML size is the original image size.

Treat a `label` marker embedded in a route's `points` sequence as the source-authoritative number position. The coordinate immediately associated with that marker is where theCrag intentionally separated the annotation from a shared start, crossing, or crowded lower wall. Use it before any automatic placement logic. This is especially important when several routes share the same lower section and diverge higher up.

When a route has no explicit `label` marker, place its compact dark, outlined number tag near the start while keeping the entire tag inside the photo. Detect collisions with other tags; choose the nearest clear candidate along or beside that route rather than stacking labels vertically over one another. Do not move an explicit theCrag label merely to normalize the layout unless it would be clipped by the image boundary.

Phototopo views may overlap. Select a small set that covers the records with a greedy non-duplicate assignment: repeatedly choose the view covering the most unassigned route numbers, assign those numbers to it, and stop when no view adds a route. Put every route that remains without geometry in a compact, clearly labeled no-topo table rather than inventing a line.

## Layout

Default table columns, in this order, are:

`★ | # | 线路名 | 难度 | 类型 | 高度 | 快挂 | 开线人 | 时间`

Keep topography and its assigned route table on the same page. Tighten margins, heading space, table padding, and leading before reducing text below practical phone readability. Use a Chinese-capable embedded font for all Chinese text, including headings, table cells, and footer.

Prefer a portrait A4 page with roughly 10 mm margins. Add a full numbered quick index before spatial topo pages so all routes are easy to locate even where one photo is not useful. For very small adjacent topo sections, combine them on one page if both photo and table pairs stay together. Do not create a page that contains just a few table rows and a large unused area when a compatible small section can share it.

Do not force every photo to fill the page: preserve aspect ratio. Prioritize the readability of numbers, line geometry, and the table over decorative space use.

## PDF production and verification

Use the available PDF workflow before authoring. Create the PDF with an embedded Chinese font and keep intermediate source, rendered topo images, and page renders outside the final deliverables location.

Validate the final artifact with all of the following:

1. Count the route rows against the parsed source; confirm each expected original number appears once in the quick index and once in a topo or no-topo section.
2. Confirm the documented number gaps are preserved and not represented as missing table rows.
3. Confirm every displayed setter/date originated from a `Set:` record, and missing data displays as `—`.
4. Render every final PDF page to PNG and visually inspect the cover, index, dense topo page, a small combined-section page, and any no-topo page. Check Chinese glyphs, text clipping, table continuation, image sharpness, narrow-line visibility, line-label contrast, label collisions, labels clipped by photo edges, footer/page numbering, and unexpected blank space. Include at least one topo with shared lower starts or explicit `label` points in this review.
5. Reopen the final PDF and verify its page count and readability before delivery.

When reporting completion, state the verified route count, any original-number gaps, how many records had `Set:` metadata, and which routes lacked topo geometry. Link or cite only the final PDF, not intermediary files.
