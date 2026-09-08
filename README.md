# Jerod Training — Block planner

A small GitHub Pages app that lays out each training block as a week you can rearrange. Same architecture as the TB2 schedule app: the plan lives in the repo, the page reads it from there, and a fine-grained token kept on your phone lets the app save back.

## Files

| File | What it is | Who writes it |
|---|---|---|
| `index.html`, `app.css`, `app.js`, `ui.js` | The app (markup, styles, core logic, rendering) | — |
| `program.json` | Every block, in order. Block 1 (v4) is already in it | Claude produces each new block; you paste it in the app |
| `state.json` | Your day moves, check-offs, and notes | The app |
| `icon.png` | Home-screen icon | — |

## One-time setup (~5 minutes, same steps as the TB2 app)

1. **New repo** on github.com named `training-block` (any name works — the app reads the repo name from its own URL). Public is fine: the plan isn't sensitive, and public keeps reading token-free.
2. **Upload** the files above to the repo root (Add file → Upload files).
3. **Enable Pages:** Settings → Pages → Source: *Deploy from a branch* → `main` / root → Save. The app appears at `https://jerod-pfeffer.github.io/training-block/` within a minute or two.
4. **Token:** the app needs a token that can write to *this* repo only. On github.com: Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token. Repository access: *Only select repositories* → `training-block`. Permissions → Repository permissions → **Contents: Read and write**. Longest expiration offered. (Your TB2 token is scoped to the TB2 repo, so it won't work here — this needs its own.)
5. **On your phone:** open the URL in Safari → Share → **Add to Home Screen**. Open it, tap ⚙, paste the token, Save. The status line at the bottom should read *Synced*.

## Using it

- **Week spine:** each row is a day. Today is marked on the left rail. Tap a card for that day's sessions; tap a session for this week's exact prescription (sets, RPE, the this-week line, video links), a **Mark done** button, and a notes box for loads and how it felt.
- **Moving days:** drag the handle on the right of a row onto another row to swap the two days, or open a day and use **Move this day**. The whole day travels — its sessions, check-offs, and notes go with it. Changes apply to that one week only; **Reset week to plan** puts it back.
- **Block view:** tap the week strip at the top (or the list icon) for the block's intro, week-by-week focus, rules, default week, baselines, and recovery notes.
- **Sync:** every change commits to `state.json`. If you're offline in the garage, the app opens from its last saved copy and tells you the change is unsaved; it saves next time it can reach GitHub. If two phones edit at once, last write wins after a re-read — same as TB2.

## Adding the next block

At the end of each block, ask for the next one **as block JSON**. Copy it, open the app → ⚙ → *Add the next block* → paste → **Add block**. The app validates it (missing day types or sessions are reported by name), commits it to `program.json`, and the matching weeks light up automatically. Re-pasting a block with the same `id` replaces the earlier version, so revisions are painless.

## Block JSON in one paragraph

A block has an `id`, `name`, optional `tag`, a Monday `start` date, a `weeks` list (`label` + `focus` per week — the count sets the block length), `intro`, `rules`, `recovery`, and `tests` text, a default `week` of seven day-type ids (Mon–Sun), the `dayTypes` those ids name (`label`, `kind` of `climb` / `lift` / `run` / `rest`, and a `sessions` list), and the `sessions` themselves (`title`, `subtitle`, `kind`, `duration`, and `blocks` of `items`). An item has `name` and any of `sets`, `effort`, `detail`, `video`, a `byWeek` map keyed `"1"`…`"n"` for prescriptions that change across the block, and a `weeks` list to show it only in some weeks. `program.json` is the reference example.
