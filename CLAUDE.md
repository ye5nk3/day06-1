# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

김분홍 방명록: a guestbook web app (name + one-line message, newest first). The whole app is a single static file, `index.html` (HTML + CSS + JS inline). There is no build step, package manager, linter, or test suite. `PRD.md` holds requirements and `STEPS.md` the staged plan (1: post/list, 2: design for phone width; both done). Docs are written in Korean.

## Run

Serve over localhost and open it in a browser (mobile width ~375px is the design target):

```bash
python3 -m http.server 8000   # then http://localhost:8000/index.html
```

## Architecture

- The page talks to Supabase directly from the browser via `@supabase/supabase-js` loaded from a CDN, using the publishable (anon) key. There is no backend.
- Backend: Supabase project **`day06`** (ref `rulmxkehqyauofgsxjlu`, not "Practice" as the PRD says; no Practice project exists), table `public.guestbook` (`id`, `name`, `message`, `created_at`). RLS is on. The `anon` role has only `select` and `insert`, granted explicitly (the RLS policies alone were not enough). The table also has length checks: name 1–30, message 1–200 after trim.
- Flow: `loadList()` selects rows ordered by `created_at` desc and renders them; the form's submit handler trims and validates, inserts, then reloads the list.

## Constraints to preserve

- User text must render literally: build DOM with `createElement` and `textContent`. Never use `innerHTML` for name or message.
- Empty or whitespace-only name/message must not be saved.
- Styling is a solid hot-pink theme (no gradients) defined as CSS variables in `:root`. Small text on the hot-pink page uses the dark ink color, since white fails contrast there.
- Schema changes go through the Supabase migration tooling; when adding tables, remember to grant privileges to `anon` in addition to RLS policies.
