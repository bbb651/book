# The widget macro

To simplify the implementation of the `Widgets` trait, let's use the relm4-macros crate!

![App screenshot dark](img/screenshots/simple-dark.png)

The app will look and behave identical to our first app from the previous chapter. Only the implementation is different.

> The app we will write in this chapter is also available [here](https://github.com/Relm4/Relm4/blob/main/relm4-examples/examples/simple.rs). Run `cargo run --example simple` from the [example directory](https://github.com/Relm4/Relm4/tree/main/relm4-examples) if you want to see the code in action.

## What's different

The `widgets` macro will take care of creating the widgets `struct` and will also implement the `Widgets` trait for us. All other parts of the code remain untouched, so we can reuse most of the code from the previous chapter.

Let's have a look at the macro and go through the code step by step:

```rust,no_run,noplayground
{{#include ../examples/simple.rs:macro }}
```

The first line doesn't change. We still have to define what's the model and what's the parent model. The only difference is that the struct `AppWidgets` is never explicitly defined in the code, but generated by the macro.

And then... wait, where do we define the `Root` type? Actually, the macro knows that your outermost widget is going to be the root widget.

Next up - the heart of the `widget` macro - the nested `view!` macro. Here, we can easily define widgets and assign properties to them.

### Properties

As you see, we start with the `gtk::ApplicationWindow` which is our root. Then we open up brackets and assign properties to the window. There's not much magic here but actually [`set_title`](https://gtk-rs.org/gtk4-rs/git/docs/gtk4/prelude/trait.GtkWindowExt.html#tymethod.set_title) is a method provided by gtk4-rs. So technically, the macro creates code like this:

```rust,no_run,noplayground
window.set_title(Some("Simple app"));
```

### Widgets

Eventually, we assign a new widget to the window.

```rust,no_run,noplayground
{{#include ../examples/simple.rs:widget_assign }}
```

The only difference to assigning properties is that we use `=` instead of `:`. We could also name widgets using the `method: name = Widget` syntax:

```rust,no_run,noplayground
            set_child: vbox = Some(&gtk::Box) {
```

Sometimes we want to use a constructor function to initialize our widgets. For the second button we used the `gtk::Button::with_label` function. This function returns a new button with the "Decrement" label already set, so we don't have to call `set_label` afterwards.

```rust,no_run,noplayground
{{#include ../examples/simple.rs:widget_assign_fn }}
```

### Events

To connect events, we use this syntax.

```rust,no_run,noplayground
method_name(cloned_var1, cloned_var2, ...) => move |args, ...| { code... }
```

Again, there's no magic. The macro will simply assign a closure to a method. Because closures often need to capture local variables that don't implement the `Copy` trait, we need to clone these variables. Therefore, we can list the variables we want to clone in the parentheses after the method name.

```rust,no_run,noplayground
{{#include ../examples/simple.rs:connect }}
```

### UI updates

The last special syntax of the `widgets` macro we'll cover here is the `watch!` macro. It's just like the normal initialization except that it also updates the property in the view function. Without it, the counter label would never be updated.

```rust,no_run,noplayground
{{#include ../examples/simple.rs:watch }}
```

> The full reference for the syntax of the widget macro can be found [here](https://relm4.org/book/stable/widget_macro_reference.html).

## The complete code

Let's review our code in one piece one more time to see how all these parts work together:

```rust,no_run,noplayground
{{#include ../examples/simple.rs:all }}
```
