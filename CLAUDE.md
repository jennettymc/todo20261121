# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A framework-free to-do tracker (fields: item, priority, deadline, requested by, notes, done). There is no build step, package manager, linter or test suite. Not a git repository.

- `index.html` is the entire app: HTML, CSS and vanilla JS in one file. Keep it that way (no frameworks, no dependencies).
- `mockup.html` is an illustrative Galaxy S24 Ultra mockup that embeds `index.html` in two iframes (`#demo` light, `#demo-dark` dark). It must sit beside `index.html`.

## Running and checking

Open `index.html` directly in a browser. `mockup.html` needs the iframes to load, so serve the folder over http (for example `python -m http.server 8765` in this folder) rather than relying on file://. To check the phone layout, resize the viewport to 640px wide or less.

## Architecture notes

- **Persistence:** tasks and theme live in `localStorage` (`tasks`, `theme`). Every access is wrapped in try/catch because storage can throw (data: URLs, private windows). `tasks = []` is the fallback. Do not remove the guards, since an uncaught throw before `tasks` is initialised breaks the whole script.
- **Theming:** CSS custom properties switched by `html[data-theme="light|dark"]`. A script in `<head>` sets the theme before first paint from the saved value or the system preference.
- **Demo mode:** when `location.hash` is `#demo` or `#demo-dark`, the app loads sample tasks, and `save()` and the theme toggle skip writing to storage, so the user's real data is never touched. The head script also forces the theme in this mode.
- **Tasks are referenced by object identity**, not by index or id (`editTask`, `tasks.indexOf(t)`, `deleteTask(t)`). `visibleTasks()` filters and stable-sorts a copy (done tasks always last), so indexes in the rendered list do not match `tasks`. Keep identity-based lookups when changing edit or delete.
- **Undo delete:** `lastDeleted = {task, index}` drives a toast that expires after 6 seconds.
- **Mobile layout:** under `@media (max-width: 640px)` the table rows become flex-wrap cards. A hidden `td.br` helper cell forces line breaks, and CSS `order` rearranges the cells, so changing the column order or adding a column needs matching changes in that media query.

## Decisions

Syncing across devices was requested and then deferred by the user ("Not now"). The idea was Firebase Firestore with a sync code and no sign-in. Do not build it unless asked.
