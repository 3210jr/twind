<div align="center">

<img src="https://twind.dev/assets/twind-logo-animated.svg" height="125" width="125" />
<a href="https://twind.dev" align="center"><h1>Twind</h1></a>

<p align="center">
The smallest, fastest, most feature complete Tailwind-in-JS solution in existence
</p>

</div>

---

[![MIT License](https://flat.badgen.net/github/license/tw-in-js/twind)](https://github.com/tw-in-js/twind/blob/next/LICENSE)
[![Latest Release](https://flat.badgen.net/npm/v/twind/next?icon=npm&label&cache=10800&color=blue)](https://www.npmjs.com/package/twind)
[![Documentation](https://flat.badgen.net/badge/icon/Documentation?icon=awesome&label)](https://twind.dev)
[![Github](https://flat.badgen.net/badge/icon/tw-in-js%2Ftwind?icon=github&label)](https://github.com/tw-in-js/twind/tree/next)
[![Discord](https://img.shields.io/discord/798324011980423188?label=chat&logo=discord)](https://discord.com/invite/2aP5NkszvD)
[![CI](https://github.com/tw-in-js/twind/actions/workflows/ci.yml/badge.svg?branch=next)](https://github.com/tw-in-js/twind/actions/workflows/ci.yml)
[![Coverage Status](https://flat.badgen.net/coveralls/c/github/tw-in-js/twind/next?icon=codecov&label&cache=10800)](https://coveralls.io/github/tw-in-js/twind?branch=next)

## READ THIS FIRST!

**Twind v1 is still in beta. Expect bugs!**

Twind v1 is a complete rewrite aiming to be compatible with Tailwind v3 classes.

---

## 🚀 Features

- 🎪 Interactive docs & demos
- 🕶 Seamless migration: Works for both Vue 3 and 2
- ⚡ Fully tree shakeable: Only take what you want
- 🦾 Type Strong: Written in Typescript
- 🔋 SSR Friendly
- 🌎 No bundler required: Usable via CDN
- 🔩 Flexible: Configurable theme, rules and varaints
- 🔌 Optional presets: Router, Firebase, RxJS, etc.

## 🦄 Quickstart

See [twind](./packages/twind/README.md) for a quick intro.

## 📦 Install

### CDN

### Demos

## Notable Changes

- [@twind/core](./packages/core) — without any rules to have a clean start
  - the `twind` factory returns `tw` as known from twind v0.16; additional it can be used to access:
    - the theme: `tw.theme(...)`
    - the target sheet: `tw.target`
    - allows to reset twind (start clean): `tw.clear()`
    - allows to remove twind (remove the associated style element): `tw.destroy()`
  - `apply` and `css` as known from twind v0.16.
  - new `cx` function to create class names
    - grouped rules are ungrouped
  - `style` — stitches like component definitions
    - creates _readable_ class names like
      - `style#1hvn013 style--variant-gray#1hvn013 style--size-sm#1hvn013 style--outlined-@sm-true#1hvn013`
    - with label: `style({ label: 'button', ... })`
      - `button#p8xtwh button--color-orange#p8xtwh button--size-small#p8xtwh button--color-orange_outlined-true$0#p8xtwh`
- [@twind/preset-tailwind](./packages/preset-tailwind) — a tailwindcss v3 compatible preset
- [twind](./packages/twind) — shim-first implementation using [@twind/preset-tailwind](./packages/preset-tailwind) and [@twind/preset-autoprefixer](./packages/preset-autoprefixer)
  - `setup` can be called as many times as you want.
  - `tw`, `apply` and `theme` as known from twind v0.16.
- grouping syntax:
  - allow trailing dash before parentheses for utilities -> `border-(md:{2 black opacity-50 hover:dashed}}`
  - support comma-separated group values — this would prevent different classNames errors during hydration:
    - `hover:~(!text-(3xl,center),!underline,italic,focus:not-italic)`
- rules and shortcuts based on ideas from [UnoCSS](https://github.com/antfu/unocss)

  ```js
  // defineConfig is optional but helps with type inference
  defineConfig({
    rules: [
      // Some rules
      ['hidden', { display: 'none' }],

      // Table Layout
      // .table-auto { table-layout: auto }
      // .table-fixed { table-layout: fixed }
      ['table-(auto|fixed)', 'tableLayout'],

      // Some shortcuts
      {
        // single utility alias
        red: 'text-red-100',

        // shortcuts to multiple utilities
        btn: 'py-2 px-4 font-semibold rounded-lg shadow-md',
        'btn-green': 'text-white bg-green-500 hover:bg-green-700',

        // dynamic shortcut — could be a rule as well
        'btn-': ({ $$ }) => `bg-${$$}-400 text-${$$}-100 py-2 px-4 rounded-lg`,
      },
    ],
  })
  ```

  There are lot of things possible. See [preset-tailwind/rules](./packages/preset-tailwind/src/rules.ts) for more examples.

- config (`defineConfig()` and `preset()`)

  - presets are executed in order they are defined
  - presets can currently not contain other presets — a work-around may by to use `defineConfig()` within the preset (not tested)
  - presets should use the `preset()` function for predictable merging
  - merging:

    - the config from a preset is appended to `preflight`, `rules`, `variants`, and `ignorelist`
    - `theme` and `theme.extend` are shallow merged (first one wins — this allows users to override preset values)
    - `tag` and `stringify` are overridden if defined by the preset (last one wins)

  - ignorelist: can be used ignore certain rules

    This following example matches class names from common CSS-in-JS libraries:

    ```js
    defineConfig({
      // emotion: `css-`
      // stitches: `c-`
      // styled-components: `sc-`and `-sc-
      // goober: `go1234567890`
      // DO NOT IGNORE rules starting with `~`, `#`, or `css#` — these may have been generated by `css()` or `style()`, or are tagged
      ignorelist: /^((css|s?c)-|_|go\d)|-sc-/,
    })
    ```

  - config [theme section function](https://tailwindcss.com/docs/theme#referencing-other-values) has a changed signature

    ```diff
    theme: {
    -  fill: (theme) => ({
    +  fill: ({ theme }) => ({
        gray: theme('colors.gray')
      })
    }
    ```

  - no implicit ordering within preflight

- comments (single and multiline)
- inline shortcut: `~` to apply/merge utilities -> `~(text(5xl,red-700),bg-red-100)`
- no more important suffix: `rule!` -> `!rule`
- styles (the generated CSS rules) are sorted predictably and stable — no matter in which order the rules are injected
- support `label` for a more readable class names (https://emotion.sh/docs/labels)
- support [theme(...)](https://tailwindcss.com/docs/functions-and-directives#theme) in property and arbitrary values
- no more `@screen sm` -> use the [tailwindcss syntax](https://tailwindcss.com/docs/functions-and-directives#screen) `@media screen(sm)`
- [@apply](https://tailwindcss.com/docs/functions-and-directives#apply) finally works as expected
- full support for color functions: `primary: ({ opacityVariable, opacityValue }) => ...`
- strict tailwindcss v3 compatibility
  - no IE 11 fallbacks (color, box-shadow, ...)
  - no more `font-*` and `text-*` shortcuts
  - no `border-tr` but `border-[xytrbl]*` still exists
  - no `bg-origin-*`
- no more `@global` — you must use `&` for nested selectors (this follows the [CSS Nesting Module](https://tabatkins.github.io/specs/css-nesting/))
- new `@layer` directive following the [Cascade Layers (CSS @layer) spec](https://www.bram.us/2021/09/15/the-future-of-css-cascade-layers-css-at-layer/)

  The following layer exist in the given order: `defaults`, `base`, `components`, `shortcuts`, `utilities`, `overrides`

  ```js
  import { css } from 'twind'

  element.className = css`
    /* rules with base are not sorted */
    @layer base {
      h1 {
        @apply text-2xl;
      }
      h2 {
        @apply text-xl;
      }
      /* ... */
    }

    @layer components {
      .select2-dropdown {
        @apply rounded-b-lg shadow-md;
      }
      .select2-search {
        @apply border border-gray-300 rounded;
      }
      .select2-results__group {
        @apply text-lg font-bold text-gray-900;
      }
      /* ... */
    }
  `
  ```

- drop IE 11 support

## TODO

- style: pass class and className through
- style: extract additional props and values from `when` for type inference
- style: merge all rules into one? maybe as option for `style({ merge: true })`
- `PrimaryButton~(bg-red-500 text-white ...)` -> `PrimaryButton#asdadf`
- rename preset-mini to preset-ext
- @twind/inject — parse HTML string for classNames, replace and inject CSS
- @twind/completions — provide autocompletion for classNames
- a package to make it easy to create lightweight versions of presets (like https://lodash.com/custom-builds)
- postcss plugin like tailwindcss for SSR

  ```css
  @twind;
  ```

## 🧱 Contribute

See the [Contributing Guide](./CONTRIBUTING.md)

## 🌸 Credits

### Contributors

Thank you to all the people who have already contributed to twind!

<a href="https://github.com/tw-in-js/twind/graphs/contributors"><img src="https://opencollective.com/twind/contributors.svg?width=890" /></a>

### Backers

Thank you to all our backers! [[Become a backer](https://opencollective.com/twind#backer)]

<a href="https://opencollective.com/twind#backers" target="_blank"><img src="https://opencollective.com/twind/backers.svg?width=890"></a>

### Sponsors

Thank you to all our sponsors! (please ask your company to also support this open source project by [becoming a sponsor](https://opencollective.com/twind#sponsor))

<a href="https://opencollective.com/twind/sponsor/0/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/0/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/1/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/1/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/2/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/2/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/3/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/3/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/4/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/4/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/5/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/5/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/6/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/6/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/7/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/7/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/8/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/8/avatar.svg"></a>
<a href="https://opencollective.com/twind/sponsor/9/website" target="_blank"><img src="https://opencollective.com/twind/sponsor/9/avatar.svg"></a>

<!-- This `CONTRIBUTING.md` is based on @nayafia's template https://github.com/nayafia/contributing-template -->

## ⚖️ License

The [MIT license](https://github.com/tw-in-js/twind/blob/main/LICENSE) governs your use of Twind.
