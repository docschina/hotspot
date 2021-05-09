# Vue 3 is now in RC!

We are very excited to announce that Vue 3.0 has entered RC (Release Candidate) stage!

Entering the RC stage means that both the API and implementation of Vue 3 core have stabilized. In principle, we do not expect to introduce new major features or breaking changes before the final release. Most official framework parts also now have v3 support. Please check [here](https://github.com/vuejs/vue-next#status-of-the-rest-of-the-framework) for the latest status.

## New Documentation

The Vue docs team have been updating our docs for v3 and it is now available at [v3.vuejs.org](https://v3.vuejs.org/)! It has been a massive undertaking and many thanks to the hard work by the docs team: @NataliaTepluhina, @bencodezen, @phanan and @sdras. The new docs has been meticulously migrated to cover differences between v2 and v3, runs on VuePress, and has improved code samples that can be edited inline.

For a quick overview on what's new and what's changed, please refer to the [Migration Guide](https://v3.vuejs.org/guide/migration/introduction.html).

Please note that the new documentation, especially the Migration Guide, is still a work in progress, and we will continue to polish it throughout the RC stage.

## Initial DevTools Support for v3

We have also published [a beta version of the new Vue Devtools with initial v3 support](https://github.com/vuejs/vue-devtools/releases/tag/v6.0.0-beta.1), thanks to the amazing work by @Akryum.

The devtool has received a major refactor to better decouple its core logic from different supported Vue versions. The interface also has a new look implemented with Tailwind CSS. Currently, only component inspection is supported - but more features will be coming soon.

~At the moment, the beta channel of Vue Devtools is still under review in the Chrome Web Store, but you can download and install the extension locally by following the instructions in the link above.~

Update: Devtools beta has been approved and now [available on the Chrome Web Store](https://chrome.google.com/webstore/detail/vuejs-devtools/ljjemllljcmogpfapbkkighbhhppjdbg) (Note: the devtools requires vue@^3.0.0-rc.1)

## Trying It Out

If you are interested in trying out Vue 3 today, there are a few ways to do it:

* Play with it on [Codepen](https://codepen.io/team/Vue/pen/KKpRVpx)

* Start a project using [Vite](https://github.com/vitejs/vite) with:

  ```bash
  npm init vite-app hello-vue3
  ```

  Vite comes with [`<script setup>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md) and [`<style vars>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md) support in Single File Components.

We are have a pending PR on [first-class v3 support in vue-cli](https://github.com/vuejs/vue-cli/pull/5637) - which will be available soon.

## Future Work

We haven't been able to complete full IE11 support for the RC, so we will continue to work on that.

At the same time, our primary focus will shift to documentation, migration, and compatibility. Our current goal is to provide clear documentation for starting new projects with v3, and help library authors upgrade their packages to support v3. The docs team will continue to polish the migration guide and v3 documentation based on feedback from the community.

Migrating non-trivial applications from v2 to v3 will likely be a much slower process. We will provide code mods and tools to help with such migrations, but in most cases, this will depend on how fast the project's dependencies can be upgraded to support Vue 3. It is also important to evaluate whether the risk and time investment of upgrading is worth it - since Vue 2 will continue to be supported. We plan to dedicate a focus period after 3.0 release to back-port features into v2 via compatibility plugins. We are already seeing success with this approach in [`@vue/composition-api`](https://github.com/vuejs/composition-api).

## Experimental Features

There are a few features that are shipped in the RC release, but have been marked experimental:

* `<Suspense>`
* [`<script setup>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md)
* [`<style vars>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md)

These features are shipped now in order to collect feedback from actual usage, but they may still receive breaking changes / major adjustments. They will likely remain experimental in 3.0, and become finalized as part of 3.1.
