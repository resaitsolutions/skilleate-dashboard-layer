# Skilleate Dashboard Layer

A [Nuxt layer](https://nuxt.com/docs/4.x/guide/going-further/layers) fork of
the [official Nuxt UI Dashboard template](https://github.com/nuxt-ui-templates/dashboard),
reworked to be **extended** by a host Nuxt project rather than run
standalone.

## What changed vs. upstream

- All pages moved from `app/pages/*` to `app/pages/app/*`, so the dashboard
  mounts under the `/app/*` prefix instead of the root `/` when a host
  project extends this layer alongside its own marketing/landing pages.
- Every internal route reference (`useDashboard.ts` shortcuts,
  `layouts/dashboard.vue` navigation + command palette, `UserMenu.vue`,
  `pages/app/index.vue`, `pages/app/settings.vue`) updated to the `/app`
  prefix.
- `app/layouts/default.vue` renamed to `app/layouts/dashboard.vue`, and
  every top-level page under `pages/app/*` now declares
  `definePageMeta({ layout: 'dashboard' })` explicitly. A host project
  extending this layer alongside another template almost certainly ships
  its own `layouts/default.vue` (for its marketing pages) — under Nuxt's
  layer priority rules the host's `default.vue` always wins the name
  collision, so this layer never relies on the `default` name and instead
  ships (and references) a uniquely-named layout that cannot be shadowed.
- `nuxt.config.ts`: added `$meta.name: 'dashboard'` (produces the
  `#layers/dashboard` alias for the host project) and switched `css` to an
  absolute path via `fileURLToPath`/`join`, since relative paths in a
  layer's `nuxt.config.ts` resolve against the **consuming project**, not
  the layer itself.

Everything else (components, composables, mock `server/api/*` handlers,
`app.config.ts` theme colors, dependencies) is unchanged from upstream.

## Usage

In the host project's `nuxt.config.ts`:

```ts
export default defineNuxtConfig({
  extends: [
    'github:resaitsolutions/skilleate-dashboard-layer'
  ]
})
```

The dashboard becomes available at `/app`, `/app/inbox`, `/app/customers`,
`/app/settings` (and its `general`/`members`/`notifications`/`security`
sub-routes) in the host project, using the host's own `app.vue` for the
root shell. This layer contributes its `pages/app/*` plus a uniquely-named
`dashboard` layout (`layouts/dashboard.vue`) that every one of those pages
opts into explicitly via `definePageMeta`, so it never depends on — and
never collides with — the host project's own `layouts/default.vue`.

Pin a tag/branch/commit for reproducible builds:

```ts
extends: ['github:resaitsolutions/skilleate-dashboard-layer#v1.0.0']
```

## Original template

Live demo of the unmodified upstream template:
[dashboard-template.nuxt.dev](https://dashboard-template.nuxt.dev/).
Documentation: [ui.nuxt.com](https://ui.nuxt.com/docs/getting-started/installation/nuxt).

## Local development of this layer

```bash
pnpm install
pnpm dev      # runs this layer as a standalone Nuxt app for local iteration
pnpm build
pnpm preview
```

Routes served standalone are still under `/app/*` (the layer was not
reverted to root-level routes), so local dev visits `http://localhost:3000/app`.
