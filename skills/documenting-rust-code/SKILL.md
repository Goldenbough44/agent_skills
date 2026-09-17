---
name: documenting-rust-code
description: Style for Rust doc comments (///) and code comments. Load this BEFORE making or planning ANY change to this codebase, not only when a comment is the explicit goal. Almost every edit, feature, bug fix, or refactor ends up touching a /// doc comment or // code comment somewhere, so treat this as a standing prerequisite for code work, not something to fetch only once you're mid-comment. Load it at the start, including in plan mode or when drafting an implementation plan, before writing the first line of code or plan text. Also use when reviewing existing comments for style.
---

# Documenting Rust Code

Follow high-quality standards like `time`, `jiff`, and `serde`.

## Grammar and mechanics

- Write full sentences: capitalize the first word, end with a period. In a summary line or a list item, a noun phrase counts as a sentence.
- A module summary can be a noun phrase. It still starts with a capital letter and ends with a period.
- Never use em dashes. Break the sentence into two, or use a comma, instead.
- Use minimal text to convey the purpose of the code. Minimal does not mean incomplete: don't cut information the reader actually needs, such as invariants, preconditions, units, or ownership requirements, for the sake of brevity.
- Avoid parentheticals. If a parenthetical would carry real content, such as a caveat, a condition, or an aside, rewrite it as its own sentence or fold it into the main sentence with a comma instead of nesting it in parens. Reserve actual parens for short, incidental asides like a unit or an abbreviation, not for clauses that carry the sentence's main point.
- Begin every doc comment with a single-line summary.
- Don't put comments on the same line as code.

## Content and tone

- State the invariant, precondition, or behavior directly rather than narrating the reasoning behind it or describing how some other function enforces it.
- Don't explain when or why to prefer a different function unless that guidance is the whole point of the comment. A short pointer to the alternative, such as a doc link, is enough; don't re-derive the caller's decision process in prose.
- Don't restate what's already conveyed by the signature, types, or a doc link target. Say the thing that isn't otherwise obvious.
- Describe the function's contract, meaning what it returns, what it requires, and what it means, rather than how it's implemented internally. Only mention the implementation approach when it's something the caller genuinely needs to choose the right function, such as its complexity, blocking behavior, or a cost/guarantee tradeoff against an alternative. Skip it for something like a constant-time lookup, where the caller never needs to reason about the mechanism to use it correctly.
- Describe return values in the main text, not in a separate `# Returns` section.
- Document all error conditions of fallible functions with `# Errors`.
- Include practical examples for public APIs.
- Don't document standard trait implementations such as `Debug`, `Display`, and `From`.

## Revising comments after a correction

- When a comment is rewritten because the design changed, such as the user saying the code shouldn't work the old way but a new one, or a type's scope or ownership shrinking, write the new comment as if describing the code fresh. State what it is now, not what it isn't, and don't contrast it with the earlier, now-wrong version, such as "this doesn't hold X" or "unlike before, this no longer does Y".
- A reader encountering the code for the first time never saw the earlier version, so a comment that exists only to rule it out gives them no information. Only mention a rejected alternative if it's a real, currently-live design decision the reader needs to know, such as "this holds a copy rather than a reference because X," not a leftover from how the code used to work.

## Rustdoc references

- Reference in-scope types and items with intra-doc link syntax: [`SomeType`], [`some_fn`].
- If the referenced item is not already in scope at that point in the file, add a reference-style link definition at the bottom of the doc comment using proper rustdoc syntax, such as [`SomeType`]: crate::module::SomeType.
- Link the item that's actually relevant to the point being made, such as a type, a specific method, or a module, rather than defaulting to the broadest or nearest available target.
- Link standard library types too, such as [`Vec`] and [`HashMap`].

```rust
/// Updates the [`User`] using [`UserUpdateStrategy`].
///
/// See [`validation::user`] for validation rules.
///
/// [`validation::user`]: crate::validation::user
```

## Scope of cross-file references

- Prefer intra-doc links ([`Type`]) over raw file paths written as prose, such as crates/server/src/frames/building.rs. A raw path goes stale silently on rename or move and creates coupling that isn't enforced by anything.
- Keep references to other files, modules, or crates scoped to what this specific piece of code actually needs the reader to know, not general "see also, related code lives here" pointers. Every reference is something a future rename has to remember to fix, so each one should earn its keep.
- This doesn't mean never reference other files. When a real dependency or invariant spans files, such as this code assuming another system has already run or a constant needing to match one defined elsewhere, say so and point at it. Don't reach for a distant file just to give extra background the reader doesn't need for this code to be correct.

## Documentation patterns

### Basic doc comment

```rust
/// Retrieves an entity by its UUID.
///
/// Loads the entity from the store and verifies access permissions.
/// Returns the [`Entity`] if it exists and is accessible.
///
/// # Errors
///
/// - [`NotFound`] if the entity doesn't exist
/// - [`AuthorizationError`] if access is denied
///
/// [`NotFound`]: EntityError::NotFound
/// [`AuthorizationError`]: EntityError::Authorization
pub fn get_entity(&self, id: EntityId) -> Result<Entity, Report<EntityError>> {
```

### Simple functions (0-2 params)

Describe parameters inline.

```rust
/// Processes the `input` elements and returns filtered results.
///
/// Takes a collection of `input` elements, applies the `filter_fn`,
/// and returns a [`Vec`] containing only matching elements.
```

### Complex functions (3+ params)

Use an explicit `# Arguments` section.

```rust
/// Merges multiple data sources with transformation rules.
///
/// # Arguments
///
/// * `sources` - Collection of data sources to merge
/// * `rules` - Transformation rules to apply
/// * `options` - Configuration controlling merge behavior
/// * `callback` - Optional function for each merged item
```

### Error documentation

```rust
/// # Errors
///
/// - [`WebAlreadyExists`] if the web ID is already in use
/// - [`AuthorizationError`] if permission denied
///
/// [`WebAlreadyExists`]: WebError::WebAlreadyExists
/// [`AuthorizationError`]: WebError::Authorization
```

### Module documentation

````rust
//! Entity management functionality.
//!
//! Main types:
//! - [`Entity`] - Core entity type
//! - [`EntityStore`] - Storage trait
//!
//! # Examples
//!
//! ```
//! use hash_graph::entity::Entity;
//! ```
````

### Examples with error handling

````rust
/// # Examples
///
/// ```rust
/// let entities = get_entities_by_type(type_id)?;
/// assert_eq!(entities.len(), 2);
/// # Ok::<(), Box<dyn core::error::Error>>(())
/// ```
````

## Verification

```bash
cargo doc --no-deps --all-features
```

## References

- references/function-documentation.md: Functions and methods documentation patterns
- references/type-documentation.md: Types, structs, enums, and traits documentation
- references/error-documentation.md: Error conditions and panics documentation
- references/examples-and-links.md: Examples and intra-doc links usage
