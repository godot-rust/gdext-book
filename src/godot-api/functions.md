<!--
  ~ Copyright (c) godot-rust; Bromeon and contributors.
  ~ This Source Code Form is subject to the terms of the Mozilla Public
  ~ License, v. 2.0. If a copy of the MPL was not distributed with this
  ~ file, You can obtain one at https://mozilla.org/MPL/2.0/.
-->

# Calling functions

In general, the gdext library maps Godot functions in a way that feels as idiomatic as possible in Rust. Sometimes, signatures differ from
GDScript, and this page will go into such differences.


## Table of contents
<!-- toc -->


## Godot classes

Godot classes are located in the `godot::classes` module. Some often-used ones like `Node`, `RefCounted`, `Node3D` etc. are additionally
re-exported in `godot::prelude`.

The majority of Godot's functionality is exposed via functions inside classes. Please don't hesitate to check out the [API docs][api-classes].


## Godot functions

As usual in Rust, functions are split into _methods_ (with a `&self`/`&mut self` receiver) and _associated functions_ (called "static functions"
in Godot).

To access Godot APIs on a `Gd<T>` pointer, simply call the method on the `Gd` object directly. This works due to `Deref` and `DerefMut` traits,
which give you an object reference through `Gd`. In a [later][book-function-objects] chapter, we'll also see how to call from and into functions
defined in Rust.

```rust
// Call with &self receiver.
let node = Node::new_alloc();
let path = node.get_path();

// Call with &mut self receiver.
let mut node = Node::new_alloc();
let other: Gd<Node> = ...;
node.add_child(other);
```

Whether a method requires a shared reference (`&T`) or an exclusive one (`&mut T`) depends on how the method is declared in the GDExtension API
(`const` or not). Note that this distinction is **informational** only and bears no safety implications, but it is useful in practice to detect
accidental modification. Technically, you could always just create another pointer via `Gd::clone()`.

Associated functions (called "static" in GDScript) are invoked on the type itself.

```rust
Node::print_orphan_nodes();
```


## Singletons

Singleton classes (not to be confused with _autoloads_, which are sometimes called singletons, too) provide a `singleton()` function to access
the one true instance. Methods are then invoked on that instance:

```rust
let input = Input::singleton();
let jump = input.is_action_pressed("jump");
let mouse_pos = input.get_mouse_position();

// Mutable actions need mut:
let mut input = input;
input.set_mouse_mode(MouseMode::CAPTURED);
```

There are [discussions][issue-singleton-no-receiver] about providing methods directly on the singleton type instead of requiring the
`singleton()` call. This would however lose the mutability information, among a few other things.


## Default parameters

GDScript supports default values for parameters. If no argument is passed, then the default value is used. As an example, we can use
[`AcceptDialog.add_button()`][godot-acceptdialog-add-button]. The GDScript signature is:

```php
Button add_button(String text, bool right=false, String action="")
```

So you can call it in the following ways from GDScript:

```php
var dialog = AcceptDialog.new()
var button0 = dialog.add_button("Yes")
var button1 = dialog.add_button("Yes", true)
var button2 = dialog.add_button("Yes", true, "confirm")
```

In Rust, we still have a base method [`AcceptDialog::add_button()`][api-acceptdialog-add-button], which takes no default arguments.
It can be called in the usual way:

```rust
let dialog = AcceptDialog::new_alloc();
let button = dialog.add_button("Yes");
```

Because Rust does not support default parameters, we have to emulate the other calls differently. We decided to use the builder pattern.

Builder methods in gdext receive **the `_ex` suffix**. Such a method takes all required parameters, like the base method. It returns a builder
object, which offers methods to set the optional parameters by their name. Eventually, a `done()` method concludes the builder and returns the
result of the Godot function call.

For our example, we have the [`AcceptDialog::add_button_ex()`][api-acceptdialog-add-button-ex] method. These two calls are exactly equivalent:

```rust
let button = dialog.add_button("Yes");
let button = dialog.add_button_ex("Yes").done();
```

You can additionally pass optional arguments using methods on the builder object. Just specify the arguments you need.
The nice thing here is that you can use any order, and skip any parameters -- unlike GDScript, where you can only skip ones at the end.

```rust
// Equivalent in GDScript: dialog.add_button("Yes", true, "")
let button = dialog.add_button_ex("Yes")
    .right(true)
    .done();

// GDScript: dialog.add_button("Yes", false, "confirm")
let button = dialog.add_button_ex("Yes")
    .action("confirm")
    .done();

// GDScript: dialog.add_button("Yes", true, "confirm")
let button = dialog.add_button_ex("Yes")
    .right(true)
    .action("confirm")
    .done();
```


## Dynamic calls

Sometimes, you want to invoke functions that are not exposed in the Rust API. These could be functions you wrote inside custom GDScript code,
or methods from other GDExtensions.

When you don't have the static information available, you can use Godot's reflection APIs. Godot provides [`Object.call()`][godot-object-call]
among others, which is exposed in two ways in Rust.

If you expect a call to succeed (since you know the GDScript code you wrote), use [`Object::call()`][api-object-call].
This method will panic if the call fails, providing a detailed message.

```rust
let node = get_node_as::<Node2D>("path/to/MyScript");

// Declare arguments as a slice of variants.
let args = &["string".to_variant(), 42.to_variant()];

// Call the method dynamically.
let val: Variant = node.call("my_method", args);

// Convert to a known type (may panic; try_to() doensn't).
let vec2 = val.to::<Vector2>();
```

If instead you want to handle the failure case, use [`Object::try_call()`][api-object-trycall]. This method returns a `Result` with the result
or a `CallError` error.

```rust
let result: Result<Variant, CallError> = node.try_call("my_method", args);

match result {
    Ok(val) => {
        let vec2 = val.to::<Vector2>();
        // ...
    }
    Err(err) => {
        godot_print!("Error calling method: {}", err);
    }
}
```

[api-acceptdialog-add-button-ex]: https://godot-rust.github.io/docs/gdext/master/godot/classes/struct.AcceptDialog.html#method.add_button_ex
[api-acceptdialog-add-button]: https://godot-rust.github.io/docs/gdext/master/godot/classes/struct.AcceptDialog.html#method.add_button
[api-classes]: https://godot-rust.github.io/docs/gdext/master/godot/classes/index.html
[api-object-call]: https://godot-rust.github.io/docs/gdext/master/godot/classes/struct.Object.html#method.call
[api-object-trycall]: https://godot-rust.github.io/docs/gdext/master/godot/classes/struct.Object.html#method.try_call
[book-function-objects]: ../register/functions.html#methods-and-object-access
[godot-acceptdialog-add-button]: https://docs.godotengine.org/en/stable/classes/class_acceptdialog.html#class-acceptdialog-method-add-button
[godot-object-call]: https://docs.godotengine.org/en/stable/classes/class_object.html#class-object-method-call
[issue-singleton-no-receiver]: https://github.com/godot-rust/gdext/issues/127
