# AGENTS.md

Blue Marble is a browser userscript for Wplace.live that overlays local image templates on the canvas and provides color filters and interface tools. Consult the [user guide](docs/README.md) and [contributing guide](docs/CONTRIBUTING.md) as needed before making changes.

## Repository layout

- [`src/main.js`](src/main.js) initializes the userscript, injects code into the page, and connects the modules. [`src/BlueMarble.meta.js`](src/BlueMarble.meta.js) declares matching URLs, execution timing, permissions, and resources.
- [`src/Template.js`](src/Template.js) handles individual templates. [`src/templateManager.js`](src/templateManager.js) manages the template collection, rendering, and persistence. [`src/settingsManager.js`](src/settingsManager.js) manages user settings.
- [`src/Overlay.js`](src/Overlay.js) provides methods for building the interface. Files matching `src/Window*.js` implement individual windows. [`src/observers.js`](src/observers.js) handles page observers. [`src/apiManager.js`](src/apiManager.js) handles page messages, site data, and telemetry.
- [`build/build.js`](build/build.js) bundles the script and styles. [`dist/`](dist/) holds the standard, Standalone, and GreasyFork builds. `dist/assets/` holds static assets used for builds and releases.
- [`docs/`](docs/) holds user and contributor documentation. The [build workflow](.github/workflows/build.yml) defines how the `main` branch builds and releases the userscript.

## Development commands

Run these commands from the repository root. Use Node.js 24 to match the build workflow.

```sh
npm ci          # Install dependencies from package-lock.json
npm run build   # Generate the scripts and styles
npm run patch   # Bump the patch version and README patch count, then build
```

Use `npm run build` for routine verification. It copies the version from `package.json` to `src/BlueMarble.meta.js` and regenerates `src/main.css` and the release artifacts in `dist/`. Edit styles in the corresponding CSS files under `src/`.

## Implementation conventions

- Make the smallest usable change work end to end through the userscript entry point before adding capabilities. Keep logic in the module responsible for the behavior and reuse existing window, template, and settings code.
- Use JavaScript ES modules and follow the existing naming and JSDoc conventions. Check the documentation and type definitions for existing dependencies before adding one. Add a maintained library when it materially reduces the code the project must maintain or improves reliability.
- When interfaces or data formats change, update affected callers directly and remove obsolete implementations. Add configuration and abstractions only when current requirements call for them.
- The injection function in `main.js` runs independently in the page context. When changing injection, the `fetch` wrapper, or `postMessage` payloads, check how both the page and the userscript handle the change.
- When changing `GM` calls or resource loading, check the permissions and resource declarations in the metadata. Telemetry requires explicit opt-in. Follow the [security policy](docs/SECURITY.md) to report vulnerabilities.

## Verification

- For changes to runtime code, styles, metadata, or build logic, run `npm run build` and check the affected release variants. For style changes, check selector references in both JavaScript and CSS. Use `dist/BlueMarble-Standalone.user.js`, which embeds the locally built CSS, for a quick browser check.
- To verify in a browser, load the local build in Tampermonkey, open Wplace.live, and confirm that the windows initialize. Depending on the change, check template import, canvas alignment, color filtering, or saving settings. For persistence changes, refresh the page and check the restored state.
- For documentation changes, check local links, paths, and formatting. Add the smallest necessary test only if it reproduces a reported regression or catches a break that would otherwise be hard to notice. Report the checks you ran and their results.

## Commits and review

- Update the relevant documentation when user-visible behavior or workflows change. For upstream contributions, follow the contributing guide to choose the target branch and fill out the [pull request template](.github/PULL_REQUEST_TEMPLATE/pull_request_template.md).
- After each coding task, use the `caveman-commit` skill to generate a Conventional Commits message.
