# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

- `npm start` / `ng serve` — dev server at http://localhost:4200 (hot reload).
- `ng build` — production build (default configuration). Output: `dist/portfolio`. Budgets: initial ≤ 1mb (warn 500kb); per-component style ≤ 4kb (warn 2kb).
- `npm run watch` — dev-config build with `--watch`.
- `ng test` — Karma + Jasmine unit tests. Run a single spec with `ng test --include=src/app/<path>/<file>.spec.ts`.
- `ng generate component <name>` — scaffolds a new component. Schematics in `angular.json` are configured so components, directives, and pipes are generated as **non-standalone** (NgModule-based) to match the existing architecture.

## Architecture

Angular 17 single-page portfolio deployed to GitHub Pages at `icaronogueira.com` (see `CNAME`). The app is intentionally simple: one route, one module, plain anchor-based navigation.

- **Module layout.** Everything is declared in `src/app/app.module.ts` (not standalone). `AppRoutingModule` has an empty `Routes` array — the SPA never changes routes. Section navigation is done by scrolling, not routing, so adding a `Routes` entry is almost certainly not what you want.
- **Shell layout (`app.component.html`).** Fixed two-column flex layout: left half holds the profile card, `app-navigation`, and social icons; right half is a vertically scrolling container with `<section id="ABOUT|SKILLS|EXPERIENCE|PROJECTS">` wrappers around the feature components. Keep section IDs uppercase — they are the contract with the navigation component.
- **Navigation flow.** `NavigationComponent` emits `(navigate)` with a section id from its hard-coded `sections` array (`ABOUT`, `SKILLS`, `EXPERIENCE`, `PROJECTS`). `AppComponent.scrollToSection` calls `scrollIntoView` and updates the URL hash via `Location.replaceState`. Adding a new section means: create the feature component, declare it in `AppModule`, add an uppercase `<section id>` wrapper in `app.component.html`, and append the id to `NavigationComponent.sections`.
- **Feature components.** `about`, `skills`, `experience`, `projects` are presentational components holding their own content/markup. `glowing-cursor` is a global visual effect mounted once at the top of `app.component.html`. `portfolio` is declared in the module but is not referenced from the shell template — treat it as unused unless proven otherwise before editing.
- **Styling.** Tailwind CSS (`tailwind.config.js` scans `src/**/*.{html,ts}`). Global styles in `src/styles.css`; each component has its own CSS file but most styling lives inline in templates as Tailwind utility classes. Dark theme is driven by `bg-slate-900 text-slate-400` on the root div.
- **Assets.** Images referenced from templates via `../assets/...` paths resolve through the `src/assets` asset mapping declared in `angular.json`.

## Deployment notes

- `CNAME` pins the custom domain for GitHub Pages — do not delete it when regenerating `dist/`.
- `localhost.crt` / `localhost.key` are for local HTTPS dev only; never ship them to production builds.
