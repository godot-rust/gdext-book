<!--
  ~ Copyright (c) godot-rust; Bromeon and contributors.
  ~ This Source Code Form is subject to the terms of the Mozilla Public
  ~ License, v. 2.0. If a copy of the MPL was not distributed with this
  ~ file, You can obtain one at https://mozilla.org/MPL/2.0/.
-->

# Ecosystem

This chapter lists third-party projects that extend godot-rust with additional functionality: tools, libraries, integrations, apps, and more.
The projects are grouped by type of project and their respective domain (although such classification is not always clear-cut).

If you'd like to add a project, please read [Contributing](#contributing)!

A list for games is also planned, and will be showcased on a separate page.


## Table of contents

<!-- toc -->

## List of 3rd-party projects


### 🏛️ Rust libraries

| Project                                                                        | Further links                                                            | Activity                                      |
|--------------------------------------------------------------------------------|--------------------------------------------------------------------------|-----------------------------------------------|
| 🌀 _**Async**_                                                                 |                                                                          |                                               |
| **[gdext-coroutines]**<br/>Integrate Rust coroutines with Godot's async/await. | [crates.io][gdext-coroutines-crate], [Discord][gdext-coroutines-discord] | ![gdext-coroutines][gdext-coroutines-badge]   |
| **[godot-tokio]**<br/>Create Tokio runtime for use with godot-rust.            | [crates.io][godot-tokio-crate], [Discord][godot-tokio-discord]           | ![godot-tokio][godot-tokio-badge]             |
| ___________________________________________________                            |                                                                          |                                               |
| 🏗️ _**Project workflow**_                                                     |                                                                          |                                               |
| **[gd-rehearse]**<br/>Unit tests for godot-rust code.                          | [Discord][gd-rehearse-discord]                                           | ![gd-rehearse][gd-rehearse-badge]             |
| **[gd-props]**<br/>Resource serialization using `serde`.                       | [Discord][gd-props-discord]                                              | ![gd-props][gd-props-badge]                   |
| **[gdext-generation]**<br/>Auto-generate the `.gdextension` file.              | [Discord][gdext-generation-discord]                                      | ![gdext-generation][gdext-generation-badge]   |
| **[godot-rust-cli]**<br/>CLI scripts for Godot with Rust.                      | [Discord][godot-rust-cli-discord]                                        | ![godot-rust-cli][godot-rust-cli-badge]   |
| ___________________________________________________                            |                                                                          |                                               |
| 📜 _**Scripting**_                                                             |                                                                          |                                               |
| **[godot-rust-script]**<br/>Allows Rust scripts to be added to nodes.          |                                                                          | ![godot-rust-script][godot-rust-script-badge] |
| ___________________________________________________                            |                                                                          |                                               |
| 🎮 _**Game development**_                                                      |                                                                          |                                               |
| **[SpireTween]**<br/>Alternative tweening library for Godot 4.2+.              | [Discord][spire-tween-discord]                                           | ![SpireTween][spire-tween-badge]              |
| **[GridForge]**<br/>Generic abstraction for grid maps.                         | [Discord][gridforge-discord]                                             | ![GridForge][gridforge-badge]                 |

[gdext-coroutines]: https://github.com/Houtamelo/gdext_coroutines
[gdext-coroutines-crate]: https://crates.io/crates/gdext_coroutines
[gdext-coroutines-discord]: https://discord.com/channels/723850269347283004/1255555232390451293/125555523
[gdext-coroutines-badge]: https://img.shields.io/github/last-commit/Houtamelo/gdext_coroutines

[godot-tokio]: https://github.com/2-3-5-41/godot_tokio
[godot-tokio-discord]: https://discord.com/channels/723850269347283004/1312490414762364928/1312490414762364928
[godot-tokio-crate]: https://crates.io/crates/godot_tokio
[godot-tokio-badge]: https://img.shields.io/github/last-commit/2-3-5-41/godot_tokio

[gd-rehearse]: https://github.com/StatisMike/gd-rehearse
[gd-rehearse-discord]: https://discord.com/channels/723850269347283004/1179891414474178661/1179891414474178661
[gd-rehearse-badge]: https://img.shields.io/github/last-commit/StatisMike/gd-rehearse

[gd-props]: https://github.com/StatisMike/gd-props
[gd-props-discord]: https://discord.com/channels/723850269347283004/1166451642145701989/1166451642145701989
[gd-props-badge]: https://img.shields.io/github/last-commit/StatisMike/gd-props

[gdext-generation]: https://github.com/sylbeth/gdext-generation
[gdext-generation-discord]: https://discord.com/channels/723850269347283004/1316664276819247124
[gdext-generation-badge]: https://img.shields.io/github/last-commit/sylbeth/gdext-generation

[godot-rust-cli]: https://github.com/TheColorRed/godot-rust
[godot-rust-cli-badge]: https://img.shields.io/github/last-commit/TheColorRed/godot-rust
[godot-rust-cli-discord]: https://discord.com/channels/723850269347283004/1325220721340977253

[godot-rust-script]: https://github.com/titannano/godot-rust-script
[godot-rust-script-badge]: https://img.shields.io/github/last-commit/titannano/godot-rust-script

[SpireTween]: https://github.com/Houtamelo/spire_tween
[spire-tween-discord]: https://discord.com/channels/723850269347283004/1257474308939452477/1257474308939452477
[spire-tween-badge]: https://img.shields.io/github/last-commit/Houtamelo/spire_tween

[GridForge]: https://github.com/StatisMike/grid-forge
[gridforge-discord]: https://discord.com/channels/723850269347283004/1238991002799444049/1238991002799444049
[gridforge-badge]: https://img.shields.io/github/last-commit/StatisMike/grid-forge


### 🧩 Editor plugins

| Project                                                                       | Further links                           | Activity                                            |
|-------------------------------------------------------------------------------|-----------------------------------------|-----------------------------------------------------|
| 📐 _**User interface**_                                                       |                                         |                                                     |
| **[Godot-Tour]**<br/>UI tours/tutorials for editor and in-game.               | [Discord][godot-tour-discord]           | ![Godot-Tour][godot-tour-badge]                     |
| ___________________________________________________                           |                                         |                                                     |
| 🎨 _**Graphics**_                                                             |                                         |                                                     |
| **[Godot Trail 3D]**<br/>Adds a `Trail3D` node to Godot.                      | [Discord][godot-trail-3d-discord]       | ![Godot Trail 3D][godot-trail-3d-badge]             |
| ___________________________________________________                           |                                         |                                                     |
| 🧲 _**Physics**_                                                              |                                         |                                                     |
| **[Godot Rapier Physics]**<br/>Rapier 2D + 3D integration for Godot.          | [Discord][godot-rapier-physics-discord] | ![Godot Rapier Physics][godot-rapier-physics-badge] |
| **[Godot Rapier 3D]**<br/>GDExtension that enables Rapier physics with Godot. | [Discord][godot-rapier-3d-discord]      | ![Godot Rapier 3D][godot-rapier-3d-badge]           |
| ___________________________________________________                           |                                         |                                                     |
| 🧙‍♂️ _**Storytelling**_                                                      |                                         |                                                     |
 | **[nobodywho]**<br/>Interact with local LLMs for interactive storytelling.    | [Discord][nobodywho-discord]            | ![nobodywho][nobodywho-badge]                       |
| ___________________________________________________                           |                                         |                                                     |
| 🏗️ _**Project workflow**_                                                    |                                         |                                                     |
| **[godot-sandbox]**<br/>Secure modding support for C++, Rust and others.      |                                         | ![godot-sandbox][godot-sandbox-badge]               |
| ___________________________________________________                           |                                         |                                                     |
| 🌐 _**Localization**_                                                        |                                         |                                                     |
| **[Fluent Translation]**<br/>Translation using Mozilla's Fluent (FTL).       | [Asset Library][godot-fluent-translation-assetlib] | ![godot-fluent-translation][godot-fluent-translation-badge] |

[Godot-Tour]: https://github.com/Decapitated/Godot-Tour
[godot-tour-discord]: https://discord.com/channels/723850269347283004/1272688558070698037/1272688558070698037
[godot-tour-badge]: https://img.shields.io/github/last-commit/Decapitated/Godot-Tour

[Godot Trail 3D]: https://github.com/SomeRanDev/Godot-Trail3D
[godot-trail-3d-discord]: https://discord.com/channels/723850269347283004/1246199893043974247/1246199893043974247
[godot-trail-3d-badge]: https://img.shields.io/github/last-commit/SomeRanDev/Godot-Trail3D

[Godot Rapier 3D]: https://github.com/deltasiege/godot-rapier-3d
[godot-rapier-3d-discord]: https://discord.com/channels/723850269347283004/1238758369767198741/1238758369767198741
[godot-rapier-3d-badge]: https://img.shields.io/github/last-commit/deltasiege/godot-rapier-3d

[Godot Rapier Physics]: https://github.com/appsinacup/godot-rapier-physics
[godot-rapier-physics-discord]: https://discord.com/channels/723850269347283004/1233345975255433266/1233345975255433266
[godot-rapier-physics-badge]: https://img.shields.io/github/last-commit/appsinacup/godot-rapier-physics

[nobodywho]: https://github.com/nobodywho-ooo/nobodywho
[nobodywho-discord]: https://discord.com/channels/723850269347283004/1309111775991693332/1309111775991693332
[nobodywho-badge]: https://img.shields.io/github/last-commit/nobodywho-ooo/nobodywho

[godot-sandbox]: https://github.com/libriscv/godot-sandbox
[godot-sandbox-badge]: https://img.shields.io/github/last-commit/libriscv/godot-sandbox

[Fluent Translation]: https://github.com/RedMser/godot-fluent-translation
[godot-fluent-translation-assetlib]: https://godotengine.org/asset-library/asset/2937
[godot-fluent-translation-badge]: https://img.shields.io/github/last-commit/RedMser/godot-fluent-translation


### 🖥️ Applications

| Project                                                                 | Further links                          | Activity                                          |
|-------------------------------------------------------------------------|----------------------------------------|---------------------------------------------------|
| 🎛️ _**Software platforms**_                                            |                                        |                                                   |
| **[Godot Boy]**<br/>Game boy emulator in Godot, written in Rust.        | [Discord][godot-boy-discord]           | ![Godot Boy][godot-boy-badge]                     |
| **[GDScript Transpiler]**<br/>Reimplements parts of GDScript in Rust.   | [Discord][gdscript-transpiler-discord] | ![GDScript Transpiler][gdscript-transpiler-badge] |
| ___________________________________________________                     |                                        |                                                   |
| 🛸 _**Tech demos**_                                                     |                                        |                                                   |
| **[Godot boids]**<br/>Addon for Godot that adds 2D/3D boids (flocking). | [Discord][godot-boids-discord]         | ???                                               |

[Godot Boy]: https://gitlab.com/greenfox/godot-boy
[godot-boy-discord]: https://discord.com/channels/723850269347283004/1230789480290586624/1230789480290586624
[godot-boy-badge]: https://img.shields.io/gitlab/last-commit/greenfox/godot-boy

[GDScript Transpiler]: https://gitlab.com/the-SSD/gdscript-transpiler
[gdscript-transpiler-badge]: https://img.shields.io/gitlab/last-commit/the-SSD/gdscript-transpiler
[gdscript-transpiler-discord]: https://discord.com/channels/723850269347283004/1237464552384499833/1237464552384499833

[Godot boids]: https://git.gaze.systems/dusk/godot_boids
[godot-boids-discord]: https://discord.com/channels/723850269347283004/1279645654439821393/1279645654439821393


## Contributing

If you have a project that might fit this list, great! You don't have to be the author -- if you've come across something that will make other
people's lives easier, please share it!

To keep this list useful for visitors, there are a few acceptance criteria:

- The project must be related to godot-rust (not only Rust or only Godot). It should use Godot 4.
- There's already something tangible with at least minimal docs/examples.
  - This could be a usable library on GitHub, a working demo, etc. No need for a crate release or very polished presentation; the idea is
    that the project is accessible for newcomers.
  - To discuss ideas and WIP prototypes, feel free to start a discussion [in `#showcase` on Discord][discord-showcase]!
- The author should be willing to maintain the project for a while.
  - GDExtension has a very good track record with binary compatibility, and [godot-rust supports extensions down to Godot 4.1][gdext-compat].
    So if you integrate via extensions (e.g. as an editor plugin), your project tends to be more future-proof than with source code.
  - That said, we don't have major breaking changes very often.
- If the project is intended for distribution and usage, make sure it comes with a license (e.g. an open-source one for software, or
  Creative Commons for artworks).

Once that's sorted, please open a pull request directly to the [book repository][book-repo]. If you're not sure about
the criteria or have other questions, don't hesitate to ask on Discord or the [book issue tracker][book-issues].

```admonish tip title="A thriving ecosystem"
Every single project enriches the space around Godot and Rust, and lets more and more people enjoy game development.
Thanks a lot to every contributor!
```

[discord-showcase]: https://discord.com/channels/723850269347283004/1163944783484563537
[gdext-compat]: ../toolchain/compatibility.md
[book-repo]: https://github.com/godot-rust/book
[book-issues]: https://github.com/godot-rust/book/issues
