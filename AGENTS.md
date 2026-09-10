# Coding agent guide

Small Go library (`github.com/vcaesar/bluge_segment_api`, package `segment`)
for interfaces between Bluge and segment file formats. Keep public APIs stable.

## Layout
- `segment.go`, `document.go`, `automaton.go`, `stats.go`: API contracts.
- `data.go`: byte-slice/file-backed `Data` and `DataReader` implementations.
- `data_test.go`: same-package, table-driven tests using standard `testing`.

## Checks
- Run from the root: `go test -race ./...`.
- Focused data tests: `go test -race -run '^TestData$' .`.
- Lint follows `.golangci.yml` and `.github/workflows/lint.yml` (v1.30).
- CI Go versions and lint configuration are legacy; follow `go.mod` for the
  required Go version and report tooling conflicts rather than downgrading.
- This is a library, with no application entry point or dedicated build script.

## Conventions
- Follow existing Go formatting, grouped imports, and explicit error handling.
- Keep changes small; preserve interface semantics and the RoaringBitmap `/v2` import.
- Add regression tests for behavior changes, covering memory and file paths.
  Use `t.Run` and `t.TempDir`; close opened files.
- Preserve license headers and follow `CONTRIBUTING.md` for copyright and AUTHORS.
- Documentation-only changes need content review, not builds or tests.

## Pitfalls
- Memory-backed data shares bytes; invalid slice bounds can panic.
- Callers must close files passed to `NewDataFile`.
- `Data.Size()` returns capacity for memory and currently zero for files.
- Postings may be reused by `Next()`; copy retained values. `Advance` must move
  strictly beyond the current document number.
- Dictionary bounds are start-inclusive/end-exclusive; stored-field visitors
  return false to stop.
