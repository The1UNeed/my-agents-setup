---
name: html-communication
description: Use when the user asks to communicate through an HTML document, or if they mention "HTML" with no additional context.
metadata:
  harness: [all]
  platform: [darwin, linux]
  scope: fleet
  requires: "npx myhtmls"
---

# HTML Communication

## When to Use

Use this skill when the user wants a plan, spec, write-up, findings, summary,
report, comparison, or set of UI mocks presented as readable HTML.

Do not use it for HTML that ships as part of a product.

## Document

Create one self-contained HTML file, capped at 512 KB.

- Write it like a spec, not a landing page: dense, scannable, no hero,
  decorative chrome, marketing voice, or em dashes.
- Default to true black (`#000`), white primary text, and dark gray only for
  secondary surfaces or accents.
- Make it mobile-readable with a responsive viewport and no fixed-width layout.
- Use semantic HTML, inline CSS, inline SVG, and HTTPS or data-URL images.
- Use an inline classic script only when interactivity materially helps. Keep
  scripted pages useful without JavaScript; the sandbox blocks storage, fetch,
  workers, frames, forms, and popups.
- In script-free files, give external links `target="_blank"` and
  `rel="noopener noreferrer"`. If any script exists, omit `target="_blank"`.

Never include external or module scripts, inline event handlers, `javascript:`
URLs, forms, frames, embeds, objects, applets, meta refresh, linked stylesheets,
secrets, private URLs, or local filesystem paths.

## UI Mocks

When the user asks for variants:

- Render real styled variants, not descriptions.
- Label them `A`, `B`, `C`... for easy selection.
- Lay them out for direct comparison.
- Keep one file across iterations so its PostPlan URL stays stable.

## Publish

The user has given standing permission to upload every artifact created or
updated with this skill. Upload is required, including in auto mode. Do not ask
for separate permission or stop at the local file.

1. Write the HTML file locally.
2. Run `npx myhtmls upload <file-path>`.
3. Report the local path and the returned myhtmls URL (use the `Raw HTML` URL
   when handing the document to another agent).

Drafts are private by default: the user views them in the browser via their
Google sign-in, and agents read them back with the Bearer key from
`~/.myhtmls`. A link alone shows anyone else a sign-in wall.

Only widen access when the user asks:
```sh
npx myhtmls visibility <file-path> --login    # any signed-in user
npx myhtmls visibility <file-path> --public   # anyone with the link
npx myhtmls visibility <file-path> --private  # back to owner-only
```
Visibility sticks to the draft across re-uploads. Say which one you set.
Re-upload the same absolute path to update the existing URL. Use
npx myhtmls upload <file-path> --new only w

If validation fails, fix the markup and retan API
key; on a 401, ask the user to run npx myhtmls auth login (or
npx myhtmls auth set <api-key>), then retryested
interactivity.

Never open a browser or claim the document is hosted before upload succeeds.
Do not verify in a browser unless the user

At the end of the HTML page, cite the harness and model that generated the HTML.