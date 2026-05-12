# Pine Script v6 Reference - Claude Instructions

**This repository is a Pine Script v6 reference library.** When the user asks you to write, fix, or upgrade Pine Script in this repo (or any project that has cloned/symlinked this repo), follow the rules below.

---

## Hard Rules (non-negotiable)

1. **Every script you write MUST start with `//@version=6`.** No exceptions. v4 and v5 syntax is forbidden.
2. **Never invent function names or signatures.** If a function isn't in `reference/functions/*.md`, it either doesn't exist in v6 or was renamed. Look it up — don't guess.
3. **Consult the relevant reference file BEFORE writing code, not after.** This is the whole point of the repo.
4. **Use `ta.*` namespace functions** for indicators (RSI, EMA, etc.). Don't reinvent them manually.
5. **Forbidden legacy keywords:** `study()` (use `indicator()`), `security()` (use `request.security()`), `iff()` (use ternary `?:`), `transp=` arg on plots (use `color.new(c, transp)`).

## Lookup Routing - which file for which task

Match the user's intent to a file before writing code:

| User asks for... | Read this file first |
|---|---|
| RSI, EMA, SMA, MACD, ATR, crossover, pivot, any math indicator | [reference/functions/ta.md](reference/functions/ta.md) |
| `plot`, `plotshape`, `plotchar`, `hline`, `bgcolor`, `barcolor`, `fill` | [reference/functions/drawing.md](reference/functions/drawing.md) |
| `line.new`, `box.new`, `label.new`, `polyline.new`, `table.new` | [reference/functions/drawing.md](reference/functions/drawing.md) |
| `strategy.entry`, `strategy.exit`, `strategy.close`, backtesting, risk mgmt | [reference/functions/strategy.md](reference/functions/strategy.md) |
| Arrays (`array.*`), maps (`map.*`), matrices (`matrix.*`) | [reference/functions/collections.md](reference/functions/collections.md) |
| `math.*`, `str.*`, `input.*`, `alert`, `color.new`, type-casts, time/date | [reference/functions/general.md](reference/functions/general.md) |
| Multi-timeframe, `request.security`, `request.financial` | [reference/functions/request.md](reference/functions/request.md) |
| Built-in vars (`open`, `close`, `volume`, `bar_index`, `syminfo.*`) | [reference/variables.md](reference/variables.md) |
| Constants (`color.red`, `shape.triangle`, `plot.style_*`) | [reference/constants.md](reference/constants.md) |
| Data types (`series`, `simple`, `int`, `float`, `bool`, `line`, `box`) | [reference/types.md](reference/types.md) |
| Keywords (`if`, `for`, `while`, `switch`, `var`, `varip`, `method`, `export`) | [reference/keywords.md](reference/keywords.md) |
| Operators (`?:`, `:=`, `==`, `+`, `-`, `[]`) | [reference/operators.md](reference/operators.md) |
| Annotations (`@version`, `@variable`, `@function`, `@param`) | [reference/annotations.md](reference/annotations.md) |
| Bar-by-bar execution, `var`/`varip` behavior, repainting | [concepts/execution_model.md](concepts/execution_model.md) |
| HTF data, lookahead, repainting prevention | [concepts/timeframes.md](concepts/timeframes.md) |
| Compile errors, "max_bars_back", "series string", "undeclared identifier" | [concepts/common_errors.md](concepts/common_errors.md) |
| Methods on objects (`obj.method()` syntax) | [concepts/methods.md](concepts/methods.md) |
| User-defined types (UDTs) | [concepts/objects.md](concepts/objects.md) |

## Pre-Write Checklist

Before writing or modifying any Pine Script in this repo, run through this:

- [ ] Identified user intent (indicator / strategy / library / fix existing)?
- [ ] Read the matching reference file(s) from the routing table above?
- [ ] Confirmed every function I plan to call actually exists in those files?
- [ ] Script starts with `//@version=6`?
- [ ] No forbidden legacy keywords (`study`, `security`, `iff`, `transp=`)?
- [ ] If using `request.security`, did I check repainting guidance in [concepts/timeframes.md](concepts/timeframes.md)?
- [ ] If declaring resources (`line.new`, `box.new`, `label.new`), did I plan a cleanup path (`.delete()` or `max_lines_count` arg on `indicator()`)?

## When the User Says "My Pine Script Has Errors"

1. Ask for the exact error message. Don't guess.
2. Open [concepts/common_errors.md](concepts/common_errors.md) and search for the error string.
3. If it's a function-signature error, open the matching reference file and compare against the actual v6 signature.
4. Common v5→v6 breakage:
   - `input(...)` without a typed namespace → must be `input.int()`, `input.float()`, etc.
   - `plot(..., transp=80)` → `plot(..., color=color.new(c, 80))`
   - `study(...)` → `indicator(...)`
   - `security(...)` → `request.security(...)`
   - Reserved word collisions (e.g. naming a var `time`, `close`, `volume`)

## Style Guidelines

- Prefer named arguments for readability: `plot(close, title="Price", color=color.blue)` over positional.
- Group inputs at the top of the script under an `// === Inputs ===` comment.
- Use `var` for state that should persist across bars; use `:=` to reassign.
- For drawing objects in indicators, set `max_lines_count`, `max_boxes_count`, `max_labels_count` on the `indicator()` call when you'll create many.

## When in Doubt

If you can't find a function in any reference file, **stop and tell the user** rather than inventing syntax. The [pinescriptv6_complete_reference.md](pinescriptv6_complete_reference.md) file (403 KB, full single-file dump) is your fallback for anything missing from the chunked files.
