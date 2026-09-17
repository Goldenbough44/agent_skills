---
name: documenting-cpp-code
description: Style for C++ doc comments (///) and code comments, based on the LLVM Coding Standards. Load this BEFORE making or planning ANY change to this codebase, not only when a comment is the explicit goal. Almost every edit, feature, bug fix, or refactor ends up touching a /// doc comment or // code comment somewhere, so treat this as a standing prerequisite for code work, not something to fetch only once you're mid-comment. Load it at the start, including in plan mode or when drafting an implementation plan, before writing the first line of code or plan text. Also use when reviewing existing comments for style.
---

# Documenting C++ Code

This skill captures the user's preferences for doc comments and code comments in this codebase. It is a living document. When the user gives feedback on a specific comment, generalize the feedback into a rule below rather than only fixing the one instance.

## Grammar and mechanics

- Write full sentences: capitalize the first word, end with a period.
- Never use em dashes. Break the sentence into two, or use a comma, instead.
- Use minimal text to convey the purpose of the code. Minimal does not mean incomplete: don't cut information the reader actually needs, such as invariants, preconditions, units, or ownership requirements, for the sake of brevity.
- Avoid parentheticals. If a parenthetical would carry real content, such as a caveat, a condition, or an aside, rewrite it as its own sentence or fold it into the main sentence with a comma instead of nesting it in parens. Reserve actual parens for short, incidental asides like a unit or an abbreviation, not for clauses that carry the sentence's main point.
- Use `///` for doc comments and `//` for other comments. Use `/* */` only for argument comments.
- Begin every doc comment with a single-line summary. Don't use `\brief`.
- Don't put comments on the same line as code. An argument comment, such as `emitName(/*Prefix=*/nullptr)`, is the exception.

## Content and tone

- State the invariant, precondition, or behavior directly rather than narrating the reasoning behind it or describing how some other function enforces it.
- Don't explain when or why to prefer a different function unless that guidance is the whole point of the comment. A short pointer to the alternative, such as a doc link, is enough; don't re-derive the caller's decision process in prose.
- Don't restate what's already conveyed by the signature, types, or a doc link target. Say the thing that isn't otherwise obvious.
- Don't start a doc comment with the name of the function or class.
- Describe the function's contract, meaning what it returns, what it requires, and what it means, rather than how it's implemented internally. Only mention the implementation approach when it's something the caller genuinely needs to choose the right function, such as its complexity, blocking behavior, or a cost/guarantee tradeoff against an alternative. Skip it for something like a constant-time lookup, where the caller never needs to reason about the mechanism to use it correctly.
- Always document the usage contract, meaning the rules a caller must follow that the compiler does not check, such as preconditions, valid ranges, units, ownership, lifetime, thread safety, and when a value is updated. State it at the declaration whenever the name and the type do not convey it. Never omit it.
- Document every public class and function.
- Describe the return value in a `\returns` paragraph when the name and the signature do not make it obvious.
- Document all error conditions of fallible functions in the `\returns` paragraph, such as when the function returns null or an error.
- Include code examples only when they show intent, usage, or behavior that is not obvious.

## Revising comments after a correction

- When a comment is rewritten because the design changed, such as the user saying the code shouldn't work the old way but a new one, or a type's scope or ownership shrinking, write the new comment as if describing the code fresh. State what it is now, not what it isn't, and don't contrast it with the earlier, now-wrong version, such as "this doesn't hold X" or "unlike before, this no longer does Y".
- A reader encountering the code for the first time never saw the earlier version, so a comment that exists only to rule it out gives them no information. Only mention a rejected alternative if it's a real, currently-live design decision the reader needs to know, such as "this holds a copy rather than a reference because X," not a leftover from how the code used to work.

## Doxygen references

- Write function names with trailing parentheses, such as `getEntity()`, so Doxygen links them.
- Qualify an item outside the current namespace, such as `storage::EntityStore`.
- Reference types and other items with `\ref` when Doxygen does not link them by name.
- Reference parameters with `\p`, such as `\p Count`. Don't use `\arg`.
- Link the item that's actually relevant to the point being made, such as a class, a specific method, or a file, rather than defaulting to the broadest or nearest available target.

```cpp
/// Updates the \ref User using the given \ref UserUpdateStrategy.
///
/// See validateUser() for validation rules.
```

## Scope of cross-file references

- Prefer Doxygen references over raw file paths written as prose, such as src/server/frames/Building.cpp. A raw path goes stale silently on rename or move and creates coupling that isn't enforced by anything.
- Keep references to other files, modules, or libraries scoped to what this specific piece of code actually needs the reader to know, not general "see also, related code lives here" pointers. Every reference is something a future rename has to remember to fix, so each one should earn its keep.
- This doesn't mean never reference other files. When a real dependency or invariant spans files, such as this code assuming another system has already run or a constant needing to match one defined elsewhere, say so and point at it. Don't reach for a distant file just to give extra background the reader doesn't need for this code to be correct.

## Documentation patterns

Put doc comments for public APIs in the header. Doc comments for private APIs can go in the implementation file. Do not repeat a doc comment in both files.

### Basic doc comment

```cpp
/// Loads an entity by its ID.
///
/// Loads the entity from the store and verifies access permissions.
///
/// \returns The entity if it exists and is accessible. The error is an
/// \ref EntityNotFoundError if the entity does not exist, and an
/// \ref AccessDeniedError if access is denied.
Expected<Entity> getEntity(EntityID ID);
```

### Simple functions (0-2 params)

Describe parameters inline.

```cpp
/// Filters the elements of \p Input with \p Pred.
///
/// \p Pred must not be empty.
///
/// \returns A new vector that contains only the matching elements.
std::vector<int> filterInts(const std::vector<int> &Input,
                            const std::function<bool(int)> &Pred);
```

### Complex functions (3+ params)

Use `\param` for each parameter. Mark an output parameter with `\param [out]` and an input/output parameter with `\param [in,out]`.

```cpp
/// Merges multiple data sources with transformation rules.
///
/// \param Sources The data sources to merge.
/// \param Rules The transformation rules to apply.
/// \param Options The configuration that controls merge behavior.
/// \param [out] Result The vector that receives the merged items on success.
///
/// \returns True on success.
bool mergeSources(const std::vector<Source> &Sources, const RuleSet &Rules,
                  const MergeOptions &Options, std::vector<Item> &Result);
```

### Error documentation

```cpp
/// Creates a web.
///
/// \returns The new web on success. The error is a \ref WebExistsError if
/// the web ID is already in use, and an \ref AccessDeniedError if permission
/// is denied.
Expected<Web> createWeb(WebID ID);
```

### Class documentation

```cpp
/// Stores entities and controls access to them.
///
/// A reference returned by the store stays valid until the entity is removed.
class EntityStore {
```

### File documentation

```cpp
//===----------------------------------------------------------------------===//
///
/// \file
/// Entity management functionality.
///
/// Main types:
/// - \ref Entity - The core entity type.
/// - \ref EntityStore - The storage interface.
///
//===----------------------------------------------------------------------===//
```

### Examples

```cpp
/// Typical usage:
/// \code
///   Expected<Entity> E = getEntity(ID);
///   if (!E)
///     return E.takeError();
/// \endcode
```

## Verification

Set `JAVADOC_AUTOBRIEF = YES` in the Doxyfile so the first sentence becomes the brief description. The Clang `-Wdocumentation` flag checks that each `\param` name matches a parameter.

```bash
doxygen Doxyfile
clang++ -fsyntax-only -Wdocumentation Entity.cpp
```
