# C Guidelines

- Use `#pragma once` for header include guards, unless the file already uses a different convention.
- Prefer `const` or `enum` over macros for constants; avoid function-like macros, unless the existing codebase already relies on them.
- Avoid undefined behavior (e.g. signed integer overflow, use of uninitialized variables, out-of-bounds access, strict aliasing violations).

## Code Style

Apply the following only when the existing file does not already follow a different convention.

- Use snake_case for variables and functions.
- Use tabs for indentation (tab width 4).
