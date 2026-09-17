---
name: documenting-python-code
description: Style for Python docstrings and code comments (#). Load this BEFORE making or planning ANY change to this codebase, not only when a comment is the explicit goal. Almost every edit, feature, bug fix, or refactor ends up touching a docstring or # code comment somewhere, so treat this as a standing prerequisite for code work, not something to fetch only once you're mid-comment. Load it at the start, including in plan mode or when drafting an implementation plan, before writing the first line of code or plan text. Also use when reviewing existing comments for style.
---

# Documenting Python Code

This skill captures the user's preferences for docstrings and code comments in this codebase.

## Grammar and mechanics

- Write full sentences: capitalize the first word, end with a period.
- Never use em dashes. Break the sentence into two, or use a comma, instead.
- Use minimal text to convey the purpose of the code. Minimal does not mean incomplete: don't cut information the reader actually needs, such as invariants, preconditions, units, or ownership requirements, for the sake of brevity.
- Avoid parentheticals. If a parenthetical would carry real content, such as a caveat, a condition, or an aside, rewrite it as its own sentence or fold it into the main sentence with a comma instead of nesting it in parens. Reserve actual parens for short, incidental asides like a unit or an abbreviation, not for clauses that carry the sentence's main point.
- Begin every docstring with a single-line summary.
- Don't put comments on the same line as code.

## Content and tone

- State the invariant, precondition, or behavior directly rather than narrating the reasoning behind it or describing how some other function enforces it.
- Don't explain when or why to prefer a different function unless that guidance is the whole point of the comment. A short pointer to the alternative, such as a doc link, is enough; don't re-derive the caller's decision process in prose.
- Don't restate what's already conveyed by the signature, type hints, or a doc link target. Say the thing that isn't otherwise obvious.
- Describe the function's contract, meaning what it returns, what it requires, and what it means, rather than how it's implemented internally. Only mention the implementation approach when it's something the caller genuinely needs to choose the right function, such as its complexity, blocking behavior, or a cost/guarantee tradeoff against an alternative. Skip it for something like a constant-time lookup, where the caller never needs to reason about the mechanism to use it correctly.
- Describe return values in the main text, not in a separate `Returns:` section.
- Document all exceptions a function raises with `Raises:`.
- Include practical examples for public APIs.
- Don't document standard dunder methods such as `__repr__`, `__eq__`, and `__hash__`.

## Revising comments after a correction

- When a comment is rewritten because the design changed, such as the user saying the code shouldn't work the old way but a new one, or a type's scope or ownership shrinking, write the new comment as if describing the code fresh. State what it is now, not what it isn't, and don't contrast it with the earlier, now-wrong version, such as "this doesn't hold X" or "unlike before, this no longer does Y".
- A reader encountering the code for the first time never saw the earlier version, so a comment that exists only to rule it out gives them no information. Only mention a rejected alternative if it's a real, currently-live design decision the reader needs to know, such as "this holds a copy rather than a reference because X," not a leftover from how the code used to work.

## Sphinx references

- Reference classes, functions, and modules with Sphinx roles: :class:`SomeType`, :func:`some_fn`, :meth:`SomeType.method`, :mod:`package.module`.
- If the referenced item is not available under that name in the current module, write its full path with a tilde, such as :class:`~package.module.SomeType`. The rendered text shows only the short name.
- Write parameter names as inline literals, such as ``count``.
- Link the item that's actually relevant to the point being made, such as a class, a specific method, or a module, rather than defaulting to the broadest or nearest available target.
- Link standard library types too, such as :class:`list` and :class:`dict`.

```python
def update_user(user: User, strategy: UserUpdateStrategy) -> None:
    """Updates the :class:`User` using :class:`UserUpdateStrategy`.

    See :func:`~app.validation.validate_user` for validation rules.
    """
```

## Scope of cross-file references

- Prefer Sphinx references (:class:`Type`) over raw file paths written as prose, such as src/server/frames/building.py. A raw path goes stale silently on rename or move and creates coupling that isn't enforced by anything.
- Keep references to other files, modules, or packages scoped to what this specific piece of code actually needs the reader to know, not general "see also, related code lives here" pointers. Every reference is something a future rename has to remember to fix, so each one should earn its keep.
- This doesn't mean never reference other files. When a real dependency or invariant spans files, such as this code assuming another system has already run or a constant needing to match one defined elsewhere, say so and point at it. Don't reach for a distant file just to give extra background the reader doesn't need for this code to be correct.

## Documentation patterns

Docstrings use Google style sections.

### Basic docstring

```python
def get_entity(self, entity_id: EntityId) -> Entity:
    """Loads an entity by its UUID.

    Loads the entity from the store and verifies access permissions.
    Returns the :class:`Entity` if it exists and is accessible.

    Raises:
        NotFoundError: The entity does not exist.
        AuthorizationError: Access is denied.
    """
```

### Simple functions (0-2 params)

Describe parameters inline.

```python
def filter_items(items: Iterable[T], predicate: Callable[[T], bool]) -> list[T]:
    """Filters ``items`` with ``predicate``.

    Returns a new :class:`list` that contains only the matching items.
    """
```

### Complex functions (3+ params)

Use an `Args:` section.

```python
def merge_sources(
    sources: Sequence[Source],
    rules: RuleSet,
    options: MergeOptions,
    callback: Callable[[Item], None] | None = None,
) -> None:
    """Merges multiple data sources with transformation rules.

    Args:
        sources: The collection of data sources to merge.
        rules: The transformation rules to apply.
        options: The configuration that controls merge behavior.
        callback: An optional function for each merged item.
    """
```

### Exception documentation

```python
def create_web(web_id: WebId) -> Web:
    """Creates a web.

    Raises:
        WebAlreadyExistsError: The web ID is already in use.
        AuthorizationError: Permission is denied.
    """
```

### Module documentation

```python
"""Entity management functionality.

Main types:

- :class:`Entity` - The core entity type.
- :class:`EntityStore` - The storage protocol.

Example:
    >>> from app.entity import Entity
"""
```

### Examples

```python
def get_entities_by_type(type_id: TypeId) -> list[Entity]:
    """Loads all entities of a type.

    Example:
        >>> entities = get_entities_by_type(type_id)
        >>> len(entities)
        2
    """
```

## Verification

Set the pydocstyle convention to `google` in the Ruff configuration. Sphinx needs `sphinx.ext.napoleon` to read Google style sections and `sphinx.ext.intersphinx` to link standard library types.

```bash
ruff check --select D .
sphinx-build -n -W docs docs/_build
```
