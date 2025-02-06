<!--
  ~ Copyright (c) godot-rust; Bromeon and contributors.
  ~ This Source Code Form is subject to the terms of the Mozilla Public
  ~ License, v. 2.0. If a copy of the MPL was not distributed with this
  ~ file, You can obtain one at https://mozilla.org/MPL/2.0/.
-->

# Hello World

This page shows you how to develop your own small extension library and load it from Godot.
The tutorial is heavily inspired by [Creating your first script][tutorial-begin] from the official Godot documentation.
We recommended to follow that alongside this tutorial, in case you're interested how certain GDScript concepts map to Rust.


## Table of contents
<!-- toc -->


## Directory setup

We assume the following file structure, with separate directories for the Godot and Rust parts:

```txt
📂 project_dir
│
├── 📂 .git
│
├── 📂 godot
│   ├── 📂 .godot
│   ├── 📄 HelloWorld.gdextension
│   └── 📄 project.godot
│
└── 📂 rust
    ├── 📄 Cargo.toml
    ├── 📂 src
    │   └── 📄 lib.rs
    └── 📂 target
        └── 📂 debug
```


## Create a Godot project

To use godot-rust, you need Godot version of 4.1 or later. Feel free to download the latest stable one. You can download in-development versions,
but we [do not provide official support for those][compatibility], so we recommend stable ones.

Open the Godot project manager and create a new Godot 4 project in the `godot/` subfolder. Add a `Sprite2D` to the center of a new scene.
We recommend that you follow the [Official tutorial][tutorial-begin] and stop at the point where it asks you to create a script.

Run your scene to make sure everything is working. Save the changes and consider versioning each step of the tutorial in Git.


## Create a Rust crate

To make a new crate with cargo, open your terminal, navigate to your desired folder and then type:

```bash
cargo new "{YourCrate}" --lib
```

where `{YourCrate}` will be used as a placeholder for a crate name of your choice. To fit with the file structure, we choose `rust` as the
crate name. `--lib` is used to create a library (not an executable), but there is some extra configuration that the crate requires.

Open `Cargo.toml` and modify it as follows:

```toml
[package]
name = "rust_project" # Part of dynamic library name; we use {YourCrate} placeholder.
version = "0.1.0"     # You can leave version and edition as-is for now.
edition = "2021"

[lib]
crate-type = ["cdylib"]  # Compile this crate to a dynamic C library.
```

The `cdylib` crate type is not very common in Rust. Instead of building an application (`bin`) or a library to be utilized by other Rust code
(`lib`), we create a _dynamic_ library, exposing an interface in the C programming language. This dynamic library is loaded by Godot at runtime,
through the GDExtension interface.

Now add gdext to your project with:

```bash
cargo add godot
```

To compile each iteration of the extension as you write code, you can use `cargo` as you normally do with any other Rust project:

```bash
cargo build
```

This should output to `{YourCrate}/target/debug/` at least one variation of a compiled library depending on your setup.


```admonish tip
If you want to follow bleeding-edge development (with the associated risks), you can directly link to the GitHub repo in the
`[dependencies]` section of your Cargo.toml. For this, replace:
~~~toml
godot = "0.x.y"
~~~
with:
~~~toml
godot = { git = "https://github.com/godot-rust/gdext", branch = "master" }
~~~
```


## Wire up Godot with Rust


### The `.gdextension` file

This file tells Godot how to load your compiled Rust extension. It contains the path to the dynamic library, as well as the
entry point (function) to initialize it with.

First, add an empty `.gdextension` file anywhere in your `godot` subfolder. In case you're familiar with Godot 3, this is the equivalent of
`.gdnlib`. In this case, we create `res://HelloWorld.gdextension` inside the `godot` subfolder and fill it as follows:

```ini
[configuration]
entry_symbol = "gdext_rust_init"
compatibility_minimum = 4.1
reloadable = true

[libraries]
linux.debug.x86_64 =     "res://../rust/target/debug/lib{YourCrate}.so"
linux.release.x86_64 =   "res://../rust/target/release/lib{YourCrate}.so"
windows.debug.x86_64 =   "res://../rust/target/debug/{YourCrate}.dll"
windows.release.x86_64 = "res://../rust/target/release/{YourCrate}.dll"
macos.debug =            "res://../rust/target/debug/lib{YourCrate}.dylib"
macos.release =          "res://../rust/target/release/lib{YourCrate}.dylib"
macos.debug.arm64 =      "res://../rust/target/debug/lib{YourCrate}.dylib"
macos.release.arm64 =    "res://../rust/target/release/lib{YourCrate}.dylib"
```

The `[configuration]` section should be copied as-is.

- Key `entry_symbol` refers to the entry point function that **gdext** exposes. We choose `"gdext_rust_init"`, which is gdext's default
  (but can be configured if needed).
- Key `compatibility_minimum` specifies the minimum version of **Godot** required by your extension to work.
  Opening the project with a version of Godot lower than this will prevent your extension from running.
  - If you build a plugin to be used by others, set this as low as possible for maximum ecosystem compatibility. This might however limit
    the features you can use.
- Key `reloadable` specifies that the editor should reload the extension when the editor window loses and
  regains focus. See [Godot issue #80284][gdextension-reloadable] for more details.
  - If Godot is crashing, you may want to try turning off or removing this setting.

The `[libraries]` section should be updated to match the paths of your dynamic Rust libraries.

- The keys on the left are the build targets of the **Godot** project.
  - Consult [GDExtension docs][godot-build-targets] for more possible values.
- The values on the right are the file paths to your dynamic library.
  - The `res://` prefix represents the path to files **relative to your Godot directory**, regardless of where your `HelloWorld.gdextension` file is.
    You can learn more about Godot's resource paths [here][godot-resource-paths].
  - If you remember the file structure, the `godot` and `rust` directories are siblings, so we need to go up one level to reach `rust`.
- You can add configurations for as many platforms as you like, if you plan to export your project to those later.
  At the very least, you need to have your current OS in `debug` mode.

```admonish tip
You can also employ the use of symbolic links and git submodules and then treat those as regular folders and files. Godot reads those just fine too! 
```

```admonish note title="Export paths"
When exporting your project, you need to use paths _inside_ `res://`.  
Outside paths like `..` are not supported. 
```

```admonish note title="Custom Rust targets"
If you specify your cargo compilation target via the `--target` flag or a `.cargo/config.toml` file, the rust library will be placed in a path name
that includes target architecture, and the `.gdextension` library paths will need to match. For example, for M1 Macs 
(`macos.debug.arm64` and `macos.release.arm64`), the path would be `"res://../rust/target/aarch64-apple-darwin/debug/lib{YourCrate}.dylib"`.
```


### `extension_list.cfg`

A second file `res://.godot/extension_list.cfg` should be generated once you open the Godot editor for the first time. This file lists all
extension registered within your project. If the file does not exist, you can also manually create it, simply containing the Godot path to
your `.gdextension` file:

```text
res://HelloWorld.gdextension
```


## Your first Rust extension

```admonish note title=".gdignore"
If you do not follow the [recommended gdext project directory setup][directory-setup] of having separate `rust/` and `godot/` directories
and instead place your rust source directly within your Godot project,
then please consider adding a [.gdignore][gd-ignore] file at the root folder of your Rust code.
This avoids cases where the Rust Compiler may produce a file in your rust folder with an ambiguous extension such as `.obj`,
which the Godot Editor may inappropriately attempt to import, resulting in an error and preventing you from building your project.
```


### Rust entry point

As mentioned earlier, our compiled C library needs to expose an _entry point_ to Godot: a C function that can be called through
the GDExtension. Setting this up requires quite some low-level [FFI][wikipedia-ffi] code, which gdext abstracts for you.

In your `lib.rs`, replace the template with the following:

```rust
use godot::prelude::*;

struct MyExtension;

#[gdextension]
unsafe impl ExtensionLibrary for MyExtension {}
```

There are multiple things going on here:

1. Place the [`prelude`][api-prelude] module from the [`godot`][api-godot] crate into scope.
   This module contains the most common symbols in the gdext API.
2. Define a struct called `MyExtension`. This is just a type tag without data or methods, you can name it however you like.
3. Implement the [`ExtensionLibrary`][api-extensionlibrary] trait for our type, and mark it with the `#[gdextension]` attribute.

The last point declares the actual GDExtension entry point, and the proc-macro attribute takes care of the low-level details.


### Troubleshooting

It's common that there are some issues with first-time setup.  Particularly, errors related to the library not being found or the `gdext_rust_init`
entry point symbol being missing or impossible to resolve come up, usually due to an incorrect initial setup.  Here are a few troubleshooting steps
that should solve the most common problems.

- Have you run `cargo build`?
- In `Cargo.toml`, have you set `crate-type = ["cdylib"]`?
- In `my-extension.gdextension`, have you set `entry_symbol = "gdext_rust_init"`?  No other symbol can work.
- Are the paths set in `my-extension.gdextension` correct?
  - Are you sure?  Double check `/rust/target/debug/` to see if the name of the `.so`/`.dll`/`.dylib` is spelled the way you expect.
  - The paths must also be relative to the directory that `project.godot` is in.  Typically it'll be `res://../rust/...`.
- Have you written the Rust code necessary to generate the entry point symbol?
  - See [above](#rust-entry-point) for how.
- Are your gdext and Godot versions compatible? See [this page][versioning] for how to select the correct versions.
- In case you use `api-custom`, do you have
  - Godot in your `PATH` as `godot4`,
  - or an environment variable called `GODOT4_BIN`, containing the path to the Godot executable?
- Is your directory structure like this below?  It's much easier when you ask for help if it is.

```txt
my-cool-project
├── godot
│   ├── project.godot
│   └── my-extension.gdextension
└── rust
    ├── Cargo.toml
    ├── src
    └── target
        └── debug
            └── (lib)?my_extension.(so|dll|dylib)
```


## Creating a Rust class

Now, let's write Rust code to define a _class_ that can be used in Godot.

Every class inherits an existing Godot-provided class (its _base class_ or just _base_).
Rust does not natively support inheritance, but the gdext API emulates it to a certain extent.


### Class declaration

In this example, we declare a class called `Player`, which inherits Sprite2D (a node type).
This can be either defined in `lib.rs` or in a separate file `player.rs`.
In case you go for the latter, don't forget to declare mod player; in your lib.rs file.

```rust
use godot::prelude::*;
use godot::classes::Sprite2D;

#[derive(GodotClass)]
#[class(base=Sprite2D)]
struct Player {
    speed: f64,
    angular_speed: f64,

    base: Base<Sprite2D>
}
```

Let's break this down.

1. The `godot` prelude contains the most common symbols. Less frequent classes are located in the [`engine`][api-class-engine] module.

2. The `#[derive]` attribute registers `Player` as a class in the Godot engine.
   See [API docs][api-derive-godotclass] for details about `#[derive(GodotClass)]`.

3. The optional `#[class]` attribute configures how the class is registered. In this case, we specify that `Player` inherits Godot's
   `Sprite2D` class. If you don't specify the `base` key, the base class will implicitly be `RefCounted`, just as if you omitted the
   `extends` keyword in GDScript.

4. We define two fields `speed` and `angular_speed` for the logic. These are regular Rust fields, no magic involved. More about their use later.

5. The `Base<T>` type is used for the `base` field, which allows `self` to access the base instance (via composition, as Rust does not have
   native inheritance). This enables two methods that can be accessed as `self.base()` and `self.base_mut()` on your type (through an extension
   trait).

   - `T` must match the declared base class. For example, `#[class(base=Sprite2D)]` implies `Base<Sprite2D>`.
   - The name can be freely chosen, but `base` is a common convention.
   - You do not _have to_ declare this field. If it is absent, you cannot access the base object from within `self`.
     This is often not a problem, e.g. in data bundles inheriting `RefCounted`.

```admonish warning title="Correct node type"
When adding an instance of your `Player` class to the scene, make sure to select node type `Player` **and not its base `Sprite2D`**.
Otherwise, your Rust logic will not run.
We will guide you to make that change to your scene later, when you're ready to test it.

If Godot fails to load a Rust class (e.g. due to an error in your extension), it may silently replace it with its base class.
Use version control (git) to check for unwanted changes in `.tscn` files.
```


### Method declaration

Now let's add some logic. We start with overriding the `init` method, also known as the constructor.
This corresponds to GDScript's `_init()` function.

```rust
use godot::classes::ISprite2D;

#[godot_api]
impl ISprite2D for Player {
    fn init(base: Base<Sprite2D>) -> Self {
        godot_print!("Hello, world!"); // Prints to the Godot console
        
        Self {
            speed: 400.0,
            angular_speed: std::f64::consts::PI,
            base,
        }
    }
}
```

Again, those are multiple pieces working together, let's go through them one by one.

1. `#[godot_api]` - this lets gdext know that the following `impl` block is part of the Rust API to expose to Godot.
   This attribute is required here; accidentally forgetting it will cause a compile error.

2. `impl ISprite2D` - each of the engine classes has a `I{ClassName}` trait, which comes with virtual functions for that
   specific class, as well as general-purpose functionality such as `init` (the constructor) or `to_string` (String conversion).
   The trait has no required methods.

3. The `init` constructor is an associated function ("static method" in other languages) that takes the base instance as argument and returns
   a constructed instance of `Self`. While the base is usually just forwarded, the constructor is the place to initialize all your other fields.
   In this example, we assign initial values `400.0` and `PI`.

Now that initialization is sorted out, we can move on to actual logic. We would like to continuously rotate the sprite, and thus override
the `process()` method. This corresponds to GDScript's `_process()`. If you need a fixed framerate, use `physics_process()` instead.

```rust
use godot::classes::ISprite2D;

#[godot_api]
impl ISprite2D for Player {
    fn init(base: Base<Sprite2D>) -> Self { /* as before */ }

    fn physics_process(&mut self, delta: f64) {
        // In GDScript, this would be: 
        // rotation += angular_speed * delta
        
        let radians = (self.angular_speed * delta) as f32;
        self.base_mut().rotate(radians);
        // The 'rotate' method requires a f32, 
        // therefore we convert 'self.angular_speed * delta' which is a f64 to a f32
    }
}
```

GDScript uses property syntax here; Rust requires explicit method calls instead. Also, access to base class methods -- such as `rotate()`
in this example -- is done via `base()` and `base_mut()` methods.

```admonish warning title="Direct field access"
Do not use the `self.base` field directly. Use `self.base()` or `self.base_mut()` instead, otherwise you won't be able to access and call
the base class methods.
```

This is a point where you can see the result. Compile your code and launch the Godot editor.
Right-click on your `Sprite2D` in the scene tree, and choose "Change Type..."
Find and choose the `Player` node type, which will be a child of `Sprite2D` in the Change Type dialog that appears.

Now, save your changes, and run the scene. The sprite should rotate at a constant speed.

![rotating sprite][img-sprite-rotating]

```admonish tip
**Launching the Godot application**

Unfortunately there is [a GDExtension limitation][issue-no-reload] that prevents recompilation while the editor is open 
before Godot 4.2. Since Godot 4.2, it is possible to hot-reload extensions. This means you can recompile your Rust code
and Godot will pick up changes, without needing to restart the editor.

However, if you don't need to modify anything in the editor itself, you can launch Godot from the command-line or even your IDE.
Check out the [command-line tutorial][godot-command-line] for more information.
```

We now add a translation component to the sprite, following [the upstream tutorial][tutorial-full-script].

```rust
use godot::classes::ISprite2D;

#[godot_api]
impl ISprite2D for Player {
    fn init(base: Base<Sprite2D>) -> Self { /* as before */ }

    fn physics_process(&mut self, delta: f64) {
        // GDScript code:
        //
        // rotation += angular_speed * delta
        // var velocity = Vector2.UP.rotated(rotation) * speed
        // position += velocity * delta
        
        let radians = (self.angular_speed * delta) as f32;
        self.base_mut().rotate(radians);

        let rotation = self.base().get_rotation();
        let velocity = Vector2::UP.rotated(rotation) * self.speed as f32;
        self.base_mut().translate(velocity * delta as f32);
        
        // or verbose: 
        // let this = self.base_mut();
        // this.set_position(
        //     this.position() + velocity * delta as f32
        // );
    }
}
```

The result should be a sprite that rotates with an offset.

![rotating translated sprite][img-sprite-moving]


### Custom Rust APIs

Say you want to add some functionality to your `Player` class, which can be called from GDScript. For this, you have a separate `impl` block, again
annotated with `#[godot_api]`. However, this time we are using an _inherent_ `impl` (i.e. without a trait name).

Concretely, we add a function to increase the speed, and a signal to notify other objects of the speed change.

```rust
#[godot_api]
impl Player {
    #[func]
    fn increase_speed(&mut self, amount: f64) {
        self.speed += amount;
        self.base_mut().emit_signal("speed_increased", &[]);
    }

    #[signal]
    fn speed_increased();
}
```

`#[godot_api]` takes again the role of exposing the API to the Godot engine. But there are also two new attributes:

- `#[func]` exposes a function to Godot. The parameters and return types are mapped to their corresponding GDScript types.
- `#[signal]` declares a signal. A signal can be emitted with the `emit_signal` method (which every Godot class provides, since it is inherited
  from `Object`).

API attributes typically follow the GDScript keyword names: `class`, `func`, `signal`, `export`, `var`, ...

That's it for the _Hello World_ tutorial! The following chapters will go into more detail about the various features that gdext provides.


[api-class-engine]: https://godot-rust.github.io/docs/gdext/master/godot/classes/index.html
[api-class-sprite2d]: https://godot-rust.github.io/docs/gdext/master/godot/classes/struct.Sprite2D.html
[api-derive-godotclass]: https://godot-rust.github.io/docs/gdext/master/godot/register/derive.GodotClass.html
[api-extensionlibrary]: https://godot-rust.github.io/docs/gdext/master/godot/prelude/trait.ExtensionLibrary.html
[api-godot]: https://godot-rust.github.io/docs/gdext/master/godot/index.html
[api-prelude]: https://godot-rust.github.io/docs/gdext/master/godot/prelude/index.html
[compatibility]: ../toolchain/compatibility.md
[directory-setup]: https://godot-rust.github.io/book/intro/hello-world.html#directory-setup
[gd-ignore]: https://docs.godotengine.org/en/stable/tutorials/best_practices/project_organization.html#ignoring-specific-folders
[gdextension-reloadable]: https://github.com/godotengine/godot/pull/80284
[godot-build-targets]: https://docs.godotengine.org/en/stable/tutorials/scripting/gdextension/gdextension_cpp_example.html#using-the-gdextension-module
[godot-command-line]: https://docs.godotengine.org/en/stable/tutorials/editor/command_line_tutorial.html
[godot-resource-paths]: https://docs.godotengine.org/en/stable/tutorials/scripting/resources.html#external-vs-built-in
[img-sprite-moving]: https://docs.godotengine.org/en/stable/_images/scripting_first_script_rotating_godot.gif
[img-sprite-rotating]: https://docs.godotengine.org/en/stable/_images/scripting_first_script_godot_turning_in_place.gif
[issue-no-reload]: https://github.com/godotengine/godot/issues/66231
[tutorial-begin]: https://docs.godotengine.org/en/stable/getting_started/step_by_step/scripting_first_script.html
[tutorial-full-script]: https://docs.godotengine.org/en/stable/getting_started/step_by_step/scripting_first_script.html#complete-script
[versioning]: https://godot-rust.github.io/book/toolchain/godot-version.html
[wikipedia-ffi]: https://en.wikipedia.org/wiki/Foreign_function_interface
