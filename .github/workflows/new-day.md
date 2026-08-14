---
emoji: 📅
description: Adds a Daily Update nav entry and dialog for the current UTC date to index.html.
on:
  schedule: daily
  workflow_dispatch:
permissions:
  contents: read
  copilot-requests: write
tools:
  edit: true
steps:
  - run: |
      mkdir -p /tmp/gh-aw
      date -u +%Y-%m-%d > /tmp/gh-aw/today.txt
safe-outputs:
  create-pull-request:
    title-prefix: "[new-day] "
    allowed-files:
      - index.html
    max: 1
---

# New Day

## Task

Read today's UTC date from `/tmp/gh-aw/today.txt` (format: `YYYY-MM-DD`).

Express it as an ordinal date string in the form `{Nth of Month}`
(e.g. `14th of August`). Use these ordinal suffixes: 1st, 2nd, 3rd,
4th–20th, 21st, 22nd, 23rd, 24th–30th, 31st.

Derive the element ID base as `{lowercase-month}-{bare-day-number}`
(e.g. `august-14`).

Open `index.html`.

1. **Check for duplicates.** If `.daily-updates-list` already contains a
   `<button>` whose `aria-controls` equals `{id-base}-dialog`, **and** a
   `<dialog>` with `id="{id-base}-dialog"` already exists anywhere in the file,
   call `noop` explaining that today's entry is already present.

2. **Add the navigation button.** Append a new `<li>` to the end of
   `.daily-updates-list`, matching the existing button structure exactly:

   ```html
   <li>
     <button
       class="daily-update-trigger"
       type="button"
       aria-haspopup="dialog"
       aria-controls="{id-base}-dialog"
       data-dialog-trigger
     >
       <span>{Nth of Month}</span>
       <span aria-hidden="true">&#8594;</span>
     </button>
   </li>
   ```

3. **Add the dialog.** Insert a new `<dialog>` immediately before the `<script>`
   tag at the bottom of `<body>`, matching the existing dialog structure exactly:

   ```html
   <dialog
     class="daily-update-dialog"
     id="{id-base}-dialog"
     aria-labelledby="{id-base}-question"
     aria-describedby="{id-base}-answer"
   >
     <article class="daily-update-dialog-content">
       <header class="daily-update-dialog-header">
         <p>Daily Update / {Nth of Month}</p>
         <form method="dialog">
           <button class="dialog-close" type="submit" aria-label="Close dialog" title="Close dialog">
             <span aria-hidden="true">&#10005;</span>
           </button>
         </form>
       </header>
       <h2 id="{id-base}-question">Daily update confirmed for {Nth of Month}</h2>
       <p id="{id-base}-answer">The daily update workflow ran successfully on {Nth of Month}.</p>
     </article>
   </dialog>
   ```

Do not modify `styles.css` or any other file. Preserve every existing daily
update entry unchanged.

## Safe Outputs

- Use `create-pull-request` to propose the edited `index.html`.
- Call `noop` with a brief explanation if today's entry already exists.
