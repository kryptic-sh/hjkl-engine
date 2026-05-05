# Changelog

All notable changes to this project will be documented in this file. The format
is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). This
project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [0.3.8] - 2026-05-05

### Fixed

- `G` now lands on the last content-bearing line rather than the phantom empty
  row produced by a trailing newline in the buffer.
- `dd` on the last line clamps the cursor to the new last row instead of leaving
  it on the phantom empty row after deletion.
- `d$` leaves the cursor on the final character of the shortened line (col
  `n-1`) rather than one past it (col `n`).
- All charwise deletes (`d<motion>`, `da"`, `daB`, etc.) apply the normal-mode
  cursor clamp on return, preventing one-past-end col values.
- `x` and `X` now write the deleted characters to the unnamed register `"` so
  that `xp` correctly round-trips the deleted character.
- Undo clamps the restored cursor to the last valid normal-mode column, fixing
  the off-by-one after `a text<Esc>u` sequences.
- `da<quote>` eats the trailing whitespace after the closing delimiter (or
  leading whitespace if no trailing exists), matching vim's `:help text-objects`
  "around" rule and avoiding double-space residue.
- `daB` / `da{` cursor off-by-one fixed: cursor now lands on the last character
  of the line preceding the deleted block.
- `diB` / `di{` on a multi-line block now uses a linewise range over the
  interior lines, preserving the newlines adjacent to `{` and `}` instead of
  collapsing the block to a single line.

## [0.3.7] - 2026-05-05

### Added

- New public module `hjkl_engine::substitute` exposing `parse_substitute`,
  `apply_substitute`, `SubstituteCmd`, `SubstFlags`, `SubstituteOutcome`, and
  `SubstError`. These types support the `:[range]s/pattern/replacement/[flags]`
  ex-command surface in TUI hosts.
- `parse_substitute` parses the `/pattern/replacement/flags` tail (delimiter
  must be `/`; flags: `g`, `i`, `I`, `c`). Empty pattern returns `None` so the
  caller can fall back to `Editor::last_search`. Replacement supports `&` (whole
  match), `\1`…`\9` (capture groups), `\\` (literal backslash), `\&` (literal
  ampersand).
- `apply_substitute` applies a `SubstituteCmd` over a 0-based inclusive
  `RangeInclusive<u32>` of buffer lines. Handles case-sensitivity precedence
  (`I` > `i` > editor `ignore_case`), updates `Editor::set_last_search` on
  success, and returns a `SubstituteOutcome` with `replacements` and
  `lines_changed` counts.
- All new items are re-exported at the crate root.

## [0.3.6] - 2026-05-05

### Fixed

- `pos_at_byte` no longer panics when the requested byte index lands inside a
  multi-byte UTF-8 codepoint. The function now rounds down to the nearest char
  boundary so the returned `Pos` points at the column of the containing char.
  Caught by the cargo-fuzz `handle_key` target on a Cyrillic seed.

## [0.3.5] - 2026-05-05

### Added

- Re-export `decode_macro` at the crate root (`hjkl_engine::decode_macro`).
  Previously only reachable via the private `input` module. Lets external
  consumers parse vim-key strings (`<Esc>`, `<C-r>`, etc.) into `Input` events
  without depending on internal module paths.

## [0.3.4] - 2026-05-04

### Docs

- Internal CHANGELOG hygiene: backfilled missing release entries and added
  reference link definitions for all version headings. No functional changes.

## [0.3.3] - 2026-05-03

### Docs

- Dropped sealed / 14-method rhetoric from the README status section. Per the
  org's "no SPEC frozen claims" stance: the trait surface keeps growing with
  semver-respecting bumps — no value in pinning the count.

## [0.3.2] - 2026-05-03

### Removed

- `SPEC.md` deleted; rustdoc on [docs.rs](https://docs.rs/hjkl-engine) is now
  the canonical API reference. All in-source references to `SPEC.md` removed.

## [0.3.1] - 2026-04-30

### Changed

- Migrated `hjkl-engine` from the `kryptic-sh/hjkl` monorepo into its own
  repository
  ([kryptic-sh/hjkl-engine](https://github.com/kryptic-sh/hjkl-engine)) with
  full git history preserved.
- Relaxed inter-crate dependency requirements from `=0.3.0` to `0.3` (caret),
  matching the standard SemVer pattern for library dependencies.
- Bumped `ratatui` to 0.30 (was 0.29) and `crossterm` to 0.29 (was 0.28).

### Added

- Standalone `LICENSE`, `.gitignore`, and `ci.yml` workflow at the repo root.

[Unreleased]: https://github.com/kryptic-sh/hjkl-engine/compare/v0.3.8...HEAD
[0.3.8]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.8
[0.3.7]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.7
[0.3.6]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.6
[0.3.5]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.5
[0.3.4]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.4
[0.3.3]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.3
[0.3.2]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.2
[0.3.1]: https://github.com/kryptic-sh/hjkl-engine/releases/tag/v0.3.1
