# grace. landing page

A SvelteKit landing page forked from [skarokin/porty](https://github.com/skarokin/porty).

The project section links to the Grace repository, releases and README documentation. The developer rows link to the project owner’s GitHub and LinkedIn profiles; portfolio remains visibly pending until a real destination is available. Each row keeps its matching icon.

The landing page’s privacy row links to a dedicated, statically prerendered policy page at `/privacy/` (or `/grace-landing-page/privacy/` on GitHub Pages). Its typography, palette, spacing and back link follow the landing page’s existing style. The Chrome companion policy’s original effective date and disclosures are preserved, and the complete text is readable without JavaScript. Publishing this checkout and changing external store/support URLs are separate steps.

The logo starts with a stepped ripple. Click it to cycle through the ASCII donut, a blocky flip, and back to the ripple; each effect plays until the next click. A blocky color wave crosses the stationary wordmark. Reduced-motion preferences show a static identity.

## Development

Requires Node.js 20.18.1 or later and pnpm 10.

```sh
pnpm install --frozen-lockfile
pnpm run dev
pnpm run check
pnpm run build
```

The static build is written to `build/`. Set `BASE_PATH` to the repository path when building for a GitHub Pages project site, or leave it unset for a custom domain at its root.

## GitHub Pages

Pull requests to `main` and pushes to `feature/**`, `feat/**` and `fix/**` run the landing-page CI: locked dependency installation, Svelte/TypeScript checks, and static builds at both the domain root and repository prefix. Both builds must produce the landing page and standalone privacy page.

Pushes to `main` run those same checks before the Pages workflow builds for the configured Pages base path and deploys. GitHub Pages uses GitHub Actions as its publishing source. A failed check prevents deployment; re-running a previous successful Pages workflow provides a rollback. Custom domains require configuration in GitHub Pages and the domain's DNS provider.

The upstream repository did not advertise a license when this fork was created. Obtain permission from its owner before release or reuse beyond applicable GitHub terms.
