{{#include ../include/header014.md}}

Relevant official examples:
[`ecs_guide`][example::ecs_guide].

---

# Bundles

You can think of Bundles like "templates" for creating entities.
They make it easy to create [entities][cb::entity] with a common set of
[components][cb::component] types.

By creating a bundle type, instead of adding your components one by one, you
can make sure that you will never accidentally forget some important component
on your entities. The Rust compiler will give an error if you do not set all
the fields of a struct, thus helping you make sure your code is correct.

Bevy provides many [built-in bundle types][builtins::bundle] that you can use
to spawn common kinds of entities.

## Creating Bundles

To create your own bundle, derive [`Bundle`] on a `struct`:

```rust,no_run,noplayground
{{#include ../code014/src/programming/bundle.rs:bundle}}
```

When you have nested bundles, everything gets flattened.
You end up with an entity that has all the included component
types. If a type appears more than once, that's an error.

### Using Bundles

You can then use your bundle when you spawn your entities:

```rust,no_run,noplayground
{{#include ../code014/src/programming/bundle.rs:bundle-spawn}}
```

If you want to have default values (similar to Bevy's bundles):

```rust,no_run,noplayground
{{#include ../code014/src/programming/bundle.rs:bundle-default}}
```

Now you can do this:

```rust,no_run,noplayground
{{#include ../code014/src/programming/bundle.rs:bundle-spawn-default}}
```

### Bundles for Removal

Bundles can also be useful to represent a set of components that you
want to be able to easily remove from an entity.

```rust,no_run,noplayground
{{#include ../code014/src/programming/bundle.rs:cleanup-bundle}}
```

```rust,no_run,noplayground
{{#include ../code014/src/programming/bundle.rs:cleanup-bundle-remove}}
```

The component types included in the bundle will be removed from the
entity, if any of them exist on the entity.

## Loose components as bundles

Technically, Bevy also considers arbitrary tuples of components as bundles:

```
(ComponentA, ComponentB, ComponentC)
```

This allows you to easily spawn an entity using a loose bunch of components (or
bundles), or add more arbitrary components when you spawn entities. However,
this way you don't have the compile-time correctness advantages that a
well-defined `struct` gives you.

```rust,no_run,noplayground
{{#include ../code014/src/programming/bundle.rs:bundle-spawn-loose}}
```

You should strongly consider creating proper `struct`s, especially if you are
likely to spawn many similar entities. It will make your code easier to maintain.

## Querying

Note that you cannot [query][cb::query] for a whole bundle. Bundles are just a
convenience when creating the entities. Query for the individual component types
that your [system][cb::system] needs to access.

This is *wrong*:

```rust,no_run,noplayground
fn my_system(query: Query<&SpriteBundle>) {
  // ...
}
```

Instead, do this:

```rust,no_run,noplayground
fn my_system(query: Query<(&Transform, &Handle<Image>)>) {
  // ...
}
```

(or whatever specific components you need in that system)

