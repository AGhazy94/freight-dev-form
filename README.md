# Studio Freight — Custom Dropdown & Drawer (Vue 3 + TypeScript)

A compact Vue 3 + TypeScript frontend demo that implements an accessible custom select (dropdown) and a right-side drawer used for a contact form. It's built with Vite and optionally uses GSAP for enhanced animations.

Key points:

- Accessible custom select with keyboard support and ARIA attributes

- Responsive drawer component with backdrop, escape-to-close, and focus management

- Lightweight, designed as a small frontend assignment/demo

Quick start

1. Install dependencies

```sh
npm install
```

1. Start dev server

```sh
npm run dev
```

1. Build for production

```sh
npm run build
```

Project layout (important files)

- `src/components/CustomSelect.vue` — custom dropdown/select
- `src/components/Drawer.vue` — right-side drawer
- `src/views/HomeView.vue` — demo page
- `src/main.ts` and `src/App.vue` — app entry and root
- `vite.config.ts`, `package.json` — build and scripts

Notes

- TypeScript and Vue 3 Composition API are used throughout.
- GSAP is optional; CSS handles core animations.
- The project is intended as a self-contained UI demo and is MIT-licensed.

Contributing / Deploy

- Run the dev server with `npm run dev` and make changes in `src/`.
- Deploy the built `dist/` folder to any static host (Vercel, Netlify, etc.).

GitHub Pages deployment (project site)

This repository includes a GitHub Actions workflow at `.github/workflows/deploy.yml` that builds the site and deploys the `dist` folder to GitHub Pages on pushes to the `dev` branch.

- Vite `base` is configured in `vite.config.ts` as `base: '/freight-dev-form/'` so assets are referenced correctly when hosted at `https://<your-user>.github.io/freight-dev-form/`.
- To deploy manually from your machine (optional):
  1.  Build the project: `npm run build`
  2.  Publish `dist` to GitHub Pages using the `gh-pages` tool: `npm run deploy` (this runs `npx gh-pages -d dist`).

If you rename the repository or use a custom domain, update the `base` value in `vite.config.ts` and, if needed, the GitHub Pages settings in your repository under Settings -> Pages.

License

MIT
