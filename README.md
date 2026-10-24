# Picture density reproduction

Astro 7.4.2 emits an extra descriptorless candidate in a `<Picture>` source's `srcset` when `densities={[1, 2]}` already includes an explicit `1x` candidate.

[View the reproduction on GitHub Pages](https://yusaku01.github.io/astro-picture-densities-repro/).

## Reproduction of duplicate 1x candidates

Use Node.js **22.23.4 or newer** and pnpm 12.4.2.

```sh
git clone https://github.com/Yusaku01/astro-picture-densities-repro.git
cd astro-picture-densities-repro
pnpm install --frozen-lockfile
pnpm build
pnpm preview
```

Open the URL shown by `pnpm preview` in your browser.
The actual source and fallback image candidates are read from the rendered elements' `srcset` attributes; the output is not hardcoded.

| `densities` | `<source>` `srcset` entries | Fallback `<img>` `srcset` entries |
| --- | --- | --- |
| `[1, 2]` | No descriptor (= `1x`), `1x`, `2x` | `1x`, `2x` |
| `[2]` (control) | No descriptor (= `1x`), `2x` | `2x` |

Expected: each density appears only once in a source's `srcset`. The base candidate can remain when there is no explicit `1x` candidate, as in the control case.

- The project uses static output and Astro's default image service, with no adapters or integrations.
- Both cases use the same generated 1200 × 840 PNG, rendered at 400 × 280.
- The [HTML standard](https://html.spec.whatwg.org/multipage/images.html#srcset-attributes) treats a descriptorless candidate as `1x` and disallows duplicate density values.
- The `site` and `base` settings support the GitHub Pages project URL.
