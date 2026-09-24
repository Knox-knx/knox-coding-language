# Knox Programming Language

> **Knox — a lightweight, interpreted programming language with clean `[ ]` block syntax, dynamic typing, and a pure C++20 runtime.**
>
> **Version 2.0 — C++ Edition (`compiler-cpp`, C++20) · Runtime `knox 1.0.0` · Language: Knox (`.knx`)**
> **Sole runtime: `compiler-cpp/build/knox`. There is no Python backend. No `*.py` remains.**

```text
╔══════════════════════════════════════════════════════════════════════════════╗
║                                                                              ║
║                    THE KNOX PROGRAMMING LANGUAGE                             ║
║                                                                              ║
║              Complete Reference Manual and Tutorial                          ║
║                                                                              ║
║                         Version 2.0 (C++ Edition)                            ║
║                                                                              ║
║              Language Specification: Knox (.knx)                             ║
║              Runtime: compiler-cpp/build/knox (C++20, CMake)                 ║
║                                                                              ║
║                          ┌─────────────────┐                                 ║
║                          │     .knx        │                                 ║
║                          └─────────────────┘                                 ║
║                                                                              ║
║                              2026                                            ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## Table of Contents

- [1. About Knox](#1-about-knox)
- [2. Features](#2-features)
- [3. Repository Structure](#3-repository-structure)
- [4. Requirements](#4-requirements)
- [5. Installation & Build](#5-installation--build)
- [6. How To Run Any `.knx` File — `knox program.knx`](#6-how-to-run-any-knx-file--knox-programknx)
- [7. CLI Contract](#7-cli-contract)
- [8. Knox IDE (Desktop)](#8-knox-ide-desktop)
- [9. Knox Codes (Android)](#9-knox-codes-android)
- [10. Language Guide (from `KNOX_PROGRAMMING_LANGUAGE_BOOK.md`)](#10-language-guide-from-knox_programming_language_bookmd)
  - [10.1 Program Structure, Comments, Identifiers](#101-program-structure-comments-identifiers)
  - [10.2 Variables & Data Types](#102-variables--data-types)
  - [10.3 Operators & BOARDMAS Precedence](#103-operators--boardmas-precedence)
  - [10.4 Input / Output — `print`, `user`, `int.user`, `str.user`](#104-input--output--print-user-intuser-struser)
  - [10.5 Conditionals — `if` / `again if` / `else`](#105-conditionals--if--again-if--else)
  - [10.6 Loops — `while`, `repeat`, `for`, `break`, `ignore`](#106-loops--while-repeat-for-break-ignore)
  - [10.7 Functions — `func`, `call`, `return`](#107-functions--func-call-return)
  - [10.8 Strings & String Methods](#108-strings--string-methods)
  - [10.9 Lists](#109-lists)
  - [10.10 Error Handling — `try` / `catch` / `_error`](#1010-error-handling--try--catch--_error)
  - [10.11 File Handling — `call file.*(...)`](#1011-file-handling--call-file)
  - [10.12 Modules — `use` / `take`](#1012-modules--use--take)
  - [10.13 Truthiness, Undefined Variables, Numbers](#1013-truthiness-undefined-variables-numbers)
- [11. Standard Library](#11-standard-library)
- [12. Complete Examples](#12-complete-examples)
- [13. Testing](#13-testing)
- [14. Architecture](#14-architecture)
- [15. Troubleshooting & FAQ](#15-troubleshooting--faq)
- [16. Quick Reference / Cheat Sheet](#16-quick-reference--cheat-sheet)
- [17. Language Book Files](#17-language-book-files)
- [18. Version History](#18-version-history)
- [19. License](#19-license)

---

## 1. About Knox

**Knox** is a dynamically typed, interpreted programming language designed for **simplicity, readability, and minimal syntax overhead**.

Design principles:

| Principle | Description |
|-----------|-------------|
| **Readability** | Code is self-documenting; syntax mirrors natural language |
| **Minimalism** | Few keywords, consistent rules, no redundant constructs |
| **Approachability** | Low barrier for beginners; familiar concepts |
| **Extensibility** | Module system (`use`/`take`) + built-in `file.*` functions enable growth |

Key design choices:

- **Explicit blocks with `[ ]`** instead of significant whitespace (no indentation bugs, easy to generate programmatically).
- **Dynamic typing** — types belong to values, not variables. Ints (`int64`), floats (`double`), strings, bools, lists, `None`.
- **First-class functions** with recursion, strict arity checking.
- **Structured error handling** with `try[...] catch[...]` + `_error`.
- **Comprehensive file I/O** via built-in `file.*` (14 functions, always available).
- **Module system** for code organization.
- **Source files use `.knx` extension**, UTF-8 without BOM.

> Full tutorial + specification live in [`KNOX_PROGRAMMING_LANGUAGE_BOOK.md`](KNOX_PROGRAMMING_LANGUAGE_BOOK.md) (v2.0 C++ Edition, 576 lines — the source of truth) and [`Knox_Programming_Language_Book.md`](Knox_Programming_Language_Book.md) (v1.0 original book). This README distills the essential syntax and everything about the language from those books.

---

## 2. Features

- Clean `[ ]` block delimiters
- Dynamic types: `int`, `float`, `string`, `bool`, `list`, `None`
- Arithmetic with full BOARDMAS/BODMAS precedence, `**` (power), `%` (Python-style)
- Comparisons: `> >= < <= == !=`
- Conditionals: `if`, `again if`, `else` (nestable)
- Loops: `while`, `repeat`, `for i = start to end` (inclusive) + `break` / `ignore` (= continue)
- Functions: `func name:params[ body ]`, `call name(a,b)` and `call name a b`, `return`, recursion
- Strings: escapes `\n \t \r \\ \"`, methods `.upper .lower .len .strip .replace .find`
- Lists: literals, `+` concat, `* int` repeat, `.len`
- Input: `user("prompt")`, `int.user("prompt")`, `str.user("prompt")`
- Output: `print expr` (Python `str()` semantics)
- Errors: `try[...] catch[...]` with `_error` string; `break`/`ignore`/`return` propagate correctly
- Files: 14 built-ins — `open close read readline readlines write writelines seek tell eof exists remove mkdir listdir` — always via `call file.*(...)`
- Modules: `use Mod;` / `take Mod use a, b;` with case-insensitive resolver, search dirs `[<program dir>, "."]`
- C++20 runtime: Lexer → Parser → AST → tree-walking Interpreter (no VM, by design), RAII only (`unique_ptr` / `shared_ptr` / `variant`), no `new`/`delete`/`malloc`/`free`
- Desktop IDE (Qt 6) + Android app (Kotlin + NDK, same C++ sources) + stdlib + examples + conformance tests

---

## 3. Repository Structure

```text
knox/
├── README.md                          # <-- this file (the only tracked file per .gitignore)
├── KNOX_PROGRAMMING_LANGUAGE_BOOK.md  # v2.0 C++ Edition book (source of truth, 576 lines)
├── Knox_Programming_Language_Book.md  # v1.0 original book (tutorial + reference)
├── compiler-cpp/                      # official C++ backend (source of truth)
│   ├── CMakeLists.txt
│   ├── README.md
│   ├── include/  (token.hpp, lexer.hpp, ast.hpp, parser.hpp, value.hpp, interpreter.hpp, errors.hpp)
│   ├── src/      (token.cpp, lexer.cpp, ast.cpp, parser.cpp, value.cpp, errors.cpp,
│   │              interpreter.cpp, filefuncs.cpp, main.cpp)
│   ├── build/    # generated by cmake (contains `knox` binary, ignored)
│   └── tests/    # .knx + .expected suites + run_tests.sh (13 groups, no Python)
├── knox-ide/                          # desktop IDE (C++20 + Qt 6, Windows/Linux)
│   ├── CMakeLists.txt
│   ├── include/ src/ resources/ cmake/ tests/
│   └── build/    # generated (ignored)
├── knox-codes/                        # Android app (Kotlin + NDK, same backend sources)
│   ├── app/ gradle/ tools/ dist/
│   └── README.md
├── stdlib/
│   └── maths.knx                      # Knox math library (BOARDMAS-compliant)
├── examples/                          # runnable .knx examples (hello, loops, functions, input, ...)
│   ├── hello.knx
│   ├── loops.knx
│   ├── functions.knx
│   ├── input.knx
│   ├── error_demo.knx
│   └── project/
├── docs/
│   ├── build-and-release.md
│   ├── knox-ide.md
│   └── knox-codes.md
├── tests/                             # cross-implementation conformance tests
│   ├── knox_validate.cpp
│   ├── test_cross_backend.sh
│   └── build/    # generated (ignored)
├── logo/
│   └── knox.png
├── *.knx                              # root demo programs
│   ├── program.knx
│   ├── all_syntax_demo.knx (411 lines — complete syntax demo)
│   ├── maths.knx / Maths.knx
│   ├── a.knx, while.knx, test_file.knx, test_file2.knx, test_import.knx
├── knox_reference.html / knox.pdf / knox_test.pdf
└── .gitignore                         # ignores EVERYTHING except README.md
```

---

## 4. Requirements

**Backend (`compiler-cpp`):**

| Requirement | Version |
|-------------|---------|
| CMake | ≥ 3.20 |
| C++ compiler | C++20 capable (GCC / Clang / MSVC) |
| OS | Linux / Windows / macOS |
| Dependencies | None (stdlib only, `std::filesystem` for paths) |

**IDE (`knox-ide`):** CMake ≥ 3.20, C++20 compiler, Qt 6 Widgets (`qt6-base-dev` on Debian/Ubuntu).

**Android (`knox-codes`):** JDK 17+ (21 recommended), Android SDK platform 34 + build-tools 34, NDK `26.3.11579264`, Gradle 8.7 (wrapper).

No Python is required to run Knox programs.

---

## 5. Installation & Build

### 5.1 Backend (required — do this once)

```bash
cd compiler-cpp
cmake -S . -B build
cmake --build build
```

Produces:

```text
compiler-cpp/build/knox
```

Verify:

```bash
./build/knox --version
# knox 1.0.0

./build/knox --help
# Usage: ./build/knox <file.knx>
# Options:
#   --version   print version
#   --help      print this help
```

### 5.2 Make `knox` available everywhere (optional)

```bash
# option A: copy to PATH
sudo cp compiler-cpp/build/knox /usr/local/bin/knox
knox program.knx
knox --version
knox --help

# option B: shell alias (no sudo)
alias knox="$PWD/compiler-cpp/build/knox"
knox program.knx
```

### 5.3 IDE

```bash
cmake -S knox-ide -B knox-ide/build
cmake --build knox-ide/build                # produces build/KnoxIDE + build/knox-backend/knox
cmake --install knox-ide/build --prefix ~/.local
~/.local/bin/KnoxIDE
# packaging:
(cd knox-ide/build && cpack)                # .deb / .tar.gz (Linux), NSIS ZIP on Windows
```

### 5.4 Android app

```bash
export ANDROID_HOME=$HOME/Android/Sdk
cd knox-codes
./gradlew clean
./gradlew assembleDebug       # APK  → dist/Knox-Codes-debug.apk
./gradlew assembleRelease     # unsigned release APK (sign with your keystore)
./gradlew bundleRelease       # AAB  → dist/Knox-Codes-release.aab
./gradlew testDebugUnitTest   # JVM unit tests
```

See [`docs/build-and-release.md`](docs/build-and-release.md) for the full release guide.

---

## 6. How To Run Any `.knx` File — `knox program.knx`

> This is the canonical way to run Knox. `python run.py ...` no longer exists.

From the repo root (`knox/`):

```bash
./compiler-cpp/build/knox program.knx
```

If you installed / aliased the binary as `knox`, it becomes literally:

```bash
knox program.knx
```

More examples:

```bash
knox hello.knx
knox all_syntax_demo.knx
knox test_file.knx
knox test_import.knx
knox /tmp/opencode/hello.knx
knox ./demo_dir/../program.knx
```

With piped stdin (for `user` / `int.user` / `str.user`):

```bash
printf '20\n' | knox program.knx
# Enter a your age : user can vote

printf '25\n' | knox compiler-cpp/tests/input/int_input.knx
```

Hello world (`examples/hello.knx`):

```knx
</> hello.knx - minimal Knox program. Run: knox hello.knx <\>
let x = 10
let y = 20
print x + y
```

Voting example (`program.knx`):

```knx
let x = int.user("Enter a your age : ")

</> this is conditinal formating block <\>

if x>=18[
    print "user can vote "
] else [
    print "user can't vote "
]
```

Run it:

```bash
knox program.knx
printf '20\n' | knox program.knx
```

---

## 7. CLI Contract

```text
knox <file.knx>
knox --version | -v
knox --help | -h
```

| Case | Output | Stream | Exit |
|------|--------|--------|------|
| `knox --version` | `knox 1.0.0` | stdout | 0 |
| `knox --help` | `Usage: ...` + Options | stdout | 0 |
| `knox` (no args) | `Usage: <prog> <file.knx>` | stdout | 1 |
| `knox nofile.knx` | `File not found: nofile.knx` | stdout | 1 |
| Lex / parse / runtime error | `Error: <message>` | stderr | 1 |
| Success | program `print` output | stdout | 0 |

Pipeline: `program.knx → Lexer → Parser → AST → tree-walking C++ Interpreter` (no bytecode VM, by design).

Module search dirs are `[<program dir>, "."]`, resolved with `std::filesystem` (Linux / Windows / macOS compatible, no hardcoded paths).

---

## 8. Knox IDE (Desktop)

**Knox IDE 1.0.0** · bundles **backend 1.0.0**. Lightweight native IDE (C++20 + Qt 6) for Windows and Linux. Programs are executed by the real C++ backend: **Run** spawns the `knox` executable (`QProcess`), **Build** lexes/parses with linked `knox_lib` without executing.

- **File → New Project…**: creates `Name/main.knx` with runnable example.
- **File → Open Project…**: any folder becomes the project in the explorer.
- Explorer: new/rename/delete files & folders, double-click to open. Tabs, `*` unsaved marker, file watching + reload prompt. `KnoxIDE [file.knx | project-dir]` CLI open.
- Editing: line numbers, Knox highlighting (matches C++ lexer), auto-indent (extra level after `[`), bracket auto-close + match highlight, current-line highlight, find/replace (regex), go-to-line, undo/redo, word wrap, font/tab settings. `Ctrl+wheel` zooms. UTF-8.
- Running: **Run (F5)** saves, streams stdout → *Output*, stderr → *Errors*, transcript → *Terminal*. **Stop (Shift+F5)** kills runaways. **Input row + Send** feeds `user/int.user/str.user` prompts. **Run → Run Configuration…** picks file + cwd. **Build (Ctrl+B)** = syntax check only.
- Backend discovery: `KNOX_EXECUTABLE` env → setting → next to IDE binary → `~/.local/bin` → `/usr/local/bin` → `/usr/bin` → `PATH`. Override in **Tools → Settings… → Knox Executable**.
- Double-click `file.knx:line[:col]` in Output/Errors to jump. Backend crash never kills the IDE (exit code reported).
- Stdlib: `stdlib/maths.knx` installs to `share/knox/stdlib`; copy next to your program (`use maths;` resolves from program dir, case-insensitively).

Full details: [`docs/knox-ide.md`](docs/knox-ide.md).

---

## 9. Knox Codes (Android)

**Knox Codes 1.0.0** · bundles **backend 1.0.0** (NDK-built from `../compiler-cpp`). No Knox code is reimplemented in Kotlin.

```text
Kotlin UI (MainActivity)
       │
       ▼
KnoxRuntime (AIDL/bind)
       │
       ▼
:knox service process → libknox_jni (same C++ sources as the CLI)
       │
       ▼
Knox execution (real piped stdin/stdout/stderr, CLI exit codes)
```

`libknox_jni` links the backend sources and runs them in a dedicated `:knox` process. Spawning the CLI via `ProcessBuilder` is impossible on modern Android (SELinux denies app exec on targetSdk 29+, verified on API 34), so the `:knox` service is the equivalent: same sources, killable runs, crash containment.

Use: ⋮ menu = New / Open / Save / Save As / Rename / Delete, Check Syntax, Undo/Redo, Find/Replace, Go to Line, font, theme. ▶ Run executes current `.knx`; stdin in input row; ■ Stop kills loops. Imports resolve from file's dir; `maths.knx` seeded in starter project so `use maths;` works immediately.

Full details: [`knox-codes/README.md`](knox-codes/README.md), [`docs/knox-codes.md`](docs/knox-codes.md).

---

## 10. Language Guide (from `KNOX_PROGRAMMING_LANGUAGE_BOOK.md`)

This section distills the book's syntax data — every construct with correct C++-era syntax. For the complete 576-line specification see [`KNOX_PROGRAMMING_LANGUAGE_BOOK.md`](KNOX_PROGRAMMING_LANGUAGE_BOOK.md) (Ch. 0, Ch. 25, Appendix C++) and the original tutorial [`Knox_Programming_Language_Book.md`](Knox_Programming_Language_Book.md).

### 10.1 Program Structure, Comments, Identifiers

- UTF-8 source, `.knx` extension. Program = sequence of statements separated by newlines. No `main()`. Execution is sequential top-to-bottom.
- **No semicolons** as statement terminators — `;` is used **only** in `use Mod;` and `take Mod use a, b;`.
- Bare expressions are **not** valid statements:
  ```knx
  1 + 1          </> INVALID - bare expression <\>
  let x = 1 + 1  </> VALID <\>
  print 1 + 1    </> VALID <\>
  ```
- **Comments — block only**, delimiters `</> ... <\>`. May appear inline (lexer skips as whitespace). **Cannot nest** — first `<\>` ends the block. `//` is **not** a comment (it is two `/` divisions).
  ```knx
  </> single-line comment <\>
  </>
  multi-line
  comment block
  <\>
  ```
  Lexer errors: `Unclosed comment block`, `Invalid character: X` → `Error: ...` on stderr, exit 1.
- **Identifiers**: start `a–z A–Z _`, continue `a–z A–Z 0–9 _`, case-sensitive, cannot be a keyword.
  ```knx
  let x = 1
  let _private = 2
  let myVariable = 3
  ```
- **Reserved keywords**:
  ```text
  let  print  if  else  again  while  repeat  for  to  break  ignore
  func  call  return  try  catch  use  take  user  int  str  true  false
  ```
  Plus `file.*` names (`file.open`, `file.read`, …).

### 10.2 Variables & Data Types

```knx
let integer = 42
let negative = -10
let float_val = 3.14
let string = "Hello World"
let multiline = "Line 1\nLine 2\nLine 3"
let bool_true = true
let bool_false = false
let list = [1, 2, 3, "four", 5.0]
```

| Type | Examples | Notes |
|------|----------|-------|
| Integer | `42`, `-10`, `0` | `int64` |
| Float | `3.14`, `-0.5`, `2.0` | `double`, `isFloat` tracked; `5.0` prints as `5.0` |
| String | `"hello"`, `"Line 1\nLine 2"` | Escapes `\n \t \r \\ \"` (Python order) |
| Boolean | `true`, `false` | Print as `True`/`False`; promote to 1/0 in arithmetic |
| List | `[1, 2, 3]`, `["a","b"]`, `[]` | Printed via `repr`: `[1, 'a', 2.5]` |
| None | (return of `file.close`, `file.remove`, …) | Prints as `None` |

`let name = expr` binds/rebinds. Typical layout:

```knx
</> imports <\>
use Maths;

</> config <\>
let MAX_RETRIES = 3

</> functions <\>
func process:data[
    return data
]

</> main logic <\>
let input = user("Enter data: ")
print call process(input)
```

### 10.3 Operators & BOARDMAS Precedence

C++ `parseExpression()` implements full BOARDMAS/BODMAS:

1. **B**rackets `( ... )` first (innermost first)
2. **O**rders `**` next (**right-associative**: `2 ** 3 ** 2 == 512` i.e. `2 ** (3**2)`)
3. **D**ivision / **M**ultiplication / **M**odulo (`/ * %`) left-to-right
4. **A**ddition / **S**ubtraction (`+ -`) left-to-right
5. Comparisons (`> >= < <= == !=`) last

```knx
print 10 + 5 * 2      </> 20  i.e. 10 + (5*2) <\>
print (10 + 5) * 2    </> 30 <\>
print 10 - 3 - 2      </> 5   i.e. (10-3)-2 <\>
print 20 / 2 / 2      </> 5.0 <\>
print 2 + 3 * 4       </> 14 <\>
print 20 - 4 / 2      </> 18.0 <\>
print 2 ** 3 ** 2     </> 512 i.e. 2 ** (3 ** 2) <\>
```

Tight unary minus (binds to next operand, Orders before unary):

```knx
print -5 + 10    </> 5 i.e. (-5)+10 <\>
print -5         </> -5 <\>
print -2 ** 2    </> -4 i.e. -(2**2) <\>
print (-2) ** 2  </> 4 <\>
print 2 ** -1    </> 0.5 <\>
```

Rules:

- `/` **always** produces float: `10/2 → 5.0`, `7/2 → 3.5`, `10/3 → 3.3333333333333335`.
- `%` is Python-style: int `%` sign follows divisor; float `%` is `a - floor(a/b)*b`. `10%3 → 1`, `10.5%3 → 1.5`.
- `**`: non-negative int exponent by loop; negative/mixed via `pow`. `2**3 → 8`.
- `+`: `str+str`, `list+list`, numbers. `*`: `str*int`, `list*int`, numbers. Mismatches → Python-like `TypeError`s.
- Lexer order matters: `file.*` before `IDENT`, `**` before `*`, `>=/<=/==/!=` before `>/<`.

> Tip: always use brackets when intent is ambiguous.

### 10.4 Input / Output — `print`, `user`, `int.user`, `str.user`

| Form | Purpose | Syntax | Example |
|------|---------|--------|---------|
| `print` | Write to stdout + `\n` | `print expression` | `print "hello"` or `print x` |
| `user` | Read line; int if it parses as int else string | `user("prompt")` | `let n = user("Name? ")` |
| `int.user` | Read line as int; on failure prints `❌ Invalid integer input` and returns `0` | `int.user("prompt")` | `let a = int.user("Age? ")` |
| `str.user` | Read line always as string | `str.user("prompt")` | `let s = str.user("Name? ")` |

Notes (`src/interpreter.cpp`):

- `print` = Python `str()`: strings raw, ints/floats/`True`/`False`/`None`/lists via `repr`. Float `5.0` stays `5.0`.
- `user` prompt → stdout (flushed), line from stdin via `getline`. Trailing `\r` stripped. Integer-looking (`+`/`-`/digits, trimmed) → int else string.
- `str.user` supports both `str.user("...")` and `STRING DOT USER` token path. Never converts.
- EOF on stdin → `Error: EOF when reading a line`.

```knx
print "Welcome to Knox!"
let name = str.user("What is your name? ")
let age = int.user("How old are you? ")
print name
print age
```

### 10.5 Conditionals — `if` / `again if` / `else`

```knx
let score = 85
if score >= 90 [
    print "Grade: A"
] again if score >= 80 [
    print "Grade: B"
] again if score >= 70 [
    print "Grade: C"
] again if score >= 60 [
    print "Grade: D"
] else [
    print "Grade: F"
]
```

Nesting:

```knx
let x = 10
let y = 20
if x > 5 [
    if y > 15 [
        print "x>5 and y>15"
    ] else [
        print "x>5 but y<=15"
    ]
] else [
    print "x<=5"
]
```

### 10.6 Loops — `while`, `repeat`, `for`, `break`, `ignore`

`ignore` = continue.

```knx
print "=== WHILE ==="
let i = 0
while i < 5 [
    print "While iteration: "
    print i
    let i = i + 1
]

let j = 0
while j < 10 [
    let j = j + 1
    if j == 3 [
        ignore
    ]
    if j == 7 [
        break
    ]
    print j
]

print "=== REPEAT ==="
repeat 3 [
    print "Repeat iteration"
]

print "=== FOR (inclusive of end) ==="
for k = 1 to 5 [
    print k
]

for m = 1 to 10 [
    if m == 5 [
        break
    ]
    print m
]
```

Rules: `for i = start to end` **inclusive** of `end`, ints (or bool→0/1) only. Non-int → `Error: '...' object cannot be interpreted as an integer`. `repeat expr` requires int/bool count; floats/strings/lists error.

### 10.7 Functions — `func`, `call`, `return`

Definition: `func name:param1 param2[ body ]` (colon, space-separated params, `[...]` body). Body may contain **any** statement (`let/print/if/while/repeat/for/return/call/func/use/take/try/break/ignore`).

Two call forms (prefer parens):

```knx
func add:a b[
    return a + b
]
print call add(5, 3)
print call add 100 200

func factorial:n[
    let result = 1
    while n > 1 [
        let result = result * n
        let n = n - 1
    ]
    return result
]
print call factorial(5)

func max:a b[
    if a > b [
        return a
    ] else [
        return b
    ]
]
print call max(10, 20)

func sum_range:start end[
    let total = 0
    for i = start to end [
        let total = total + i
    ]
    return total
]
print call sum_range(1, 10)

func fibonacci:n[
    if n <= 1 [
        return n
    ] else [
        return call fibonacci(n - 1) + call fibonacci(n - 2)
    ]
]
print call fibonacci(8)

func greet:name[
    let msg = "Hello, " + name + "!"
    return msg
]
print call greet("Knox")
```

Semantics: functions available after `func` executes (sequential, not hoisted — define before `call`). Arity strictly checked. Scope saved/restored per call. No `return` → `None` (`return` via `ReturnSignal`).

```text
Error: Undefined function 'foo'
Error: Expected 2 arguments but got 1
```

> C++ quirk: space-form `call` argument parsing stops at `let/if/while/for/repeat/func/use/take/print/break/ignore/return/]/;/else/EOF` but **not** at `try`/`catch`/`call`. A space-form call immediately followed by a `try` or `call` line can swallow it and raise `Error: Unexpected token: TRY/CALL`. Prefer paren-form.

### 10.8 Strings & String Methods

Escapes: `\n \t \r \\ \"` (Python order). `"Line 1\nLine 2"` prints two lines.

Methods on literal or variable. `.upper` / `.lower` / `.len` / `.strip` accept optional trailing `()`: both `s.upper` and `s.upper()` work. `.len` also works on lists. Unknown method → `Error: Unknown string method: ...`.

```knx
let s = "  Hello World  "
print s.upper              </> "  HELLO WORLD  " <\>
print s.lower              </> "  hello world  " <\>
print s.len
print s.strip              </> "Hello World" <\>
print s.replace("World", "Knox")
print s.find("World")      </> index or -1 <\>
print "Hello, " + "Knox" + "!"
```

| Method | Example | Result |
|--------|---------|--------|
| `.upper` | `s.upper` | uppercased |
| `.lower` | `s.lower` | lowercased |
| `.len` | `s.len` | length (strings + lists) |
| `.strip` | `s.strip` | trim whitespace |
| `.replace("old","new")` | `s.replace("World","Knox")` | replaced |
| `.find("sub")` | `s.find("World")` | index or `-1` |

### 10.9 Lists

```knx
let mylist = [10, 20, 30, 40, 50]
print mylist
let lines = call file.readlines(fr)
print lines                </> ['Line 1\n', 'Line 2\n'] <\>
print s.len                </> works on lists too <\>
```

`+` supports `list + list`, `*` supports `list * int`.

### 10.10 Error Handling — `try` / `catch` / `_error`

Both `try[` and `try [` work. `_error` holds the message string (e.g. `division by zero`, `Undefined function 'f'`, `Invalid file handle: 9`). Nested `try` works. `break`/`ignore`/`return` inside `try` propagate outward instead of being caught.

```knx
try [
    let result = 10 / 0
    print "This won't print"
] catch [
    print "Caught division by zero: "
    print _error
]
</> division by zero <\>

func divide:a b[
    return a / b
]
try [
    let result = call divide(10, 0)
] catch [
    print _error
]

try [
    let result = 10 / 2
    print result
] catch [
    print "This won't run"
]

try [
    try [
        let x = 10 / 0
    ] catch [
        print _error
    ]
] catch [
    print "Outer catch (won't run)"
]
```

### 10.11 File Handling — `call file.*(...)`

All file functions **must be invoked with `call`**, including inside expressions. They are **not** bare identifiers. Available without any import.

Correct:

```knx
let f = call file.open("data.txt", "w")
call file.write(f, "hello\n")
call file.close(f)

let fr = call file.open("data.txt", "r")
let text = call file.read(fr)
call file.close(fr)

print call file.exists("data.txt")
```

Wrong (old Python docs style, INVALID in C++):

```knx
</> INVALID in C++ — missing call + parens <\>
let f = file.open "data.txt" "r"
call file.close f
if file.exists "data.txt"[
]
```

| Function | Purpose | C++ syntax | Example |
|----------|---------|------------|---------|
| `file.open` | Open file, return integer handle | `call file.open(filename, [mode])` | `let f = call file.open("data.txt", "r")` |
| `file.close` | Close handle | `call file.close(handle)` | `call file.close(f)` |
| `file.read` | Read all / N chars | `call file.read(handle)` or `call file.read(handle, size)` | `let t = call file.read(f)` |
| `file.readline` | Read one line (keeps `\n`) | `call file.readline(handle)` | `let l = call file.readline(f)` |
| `file.readlines` | Read all lines as list | `call file.readlines(handle)` | `let ls = call file.readlines(f)` |
| `file.write` | Write text, returns byte count | `call file.write(handle, text)` | `call file.write(f, "hi\n")` |
| `file.writelines` | Write list of strings | `call file.writelines(handle, list)` | `call file.writelines(f, ["a\n", "b\n"])` |
| `file.seek` | Seek | `call file.seek(handle, offset, [whence])` | `call file.seek(f, 20)` |
| `file.tell` | Current position | `call file.tell(handle)` | `let p = call file.tell(f)` |
| `file.eof` | EOF check (bool) | `call file.eof(handle)` | `if call file.eof(f)[ print "end" ]` |
| `file.exists` | Path exists (1/0) | `call file.exists(path)` | `if call file.exists("a.txt")[ print "found" ]` |
| `file.remove` | Remove file | `call file.remove(path)` | `call file.remove("old.txt")` |
| `file.mkdir` | Create dirs (recursive) | `call file.mkdir(path)` | `call file.mkdir("demo_dir")` |
| `file.listdir` | List dir filenames | `call file.listdir(path)` | `let d = call file.listdir(".")` |

C++ details:

- Handles are `int64` IDs (`1, 2, ...`), `std::shared_ptr<std::fstream>`. Invalid → `Error: Invalid file handle: N`.
- Modes: `r w a r+ w+ a+` (+ `b` for binary). Default `r`. Missing file on read → `Error: No such file or directory: '...'`.
- `file.read(handle, size)` reads exactly `size` bytes (or fewer at EOF).
- `file.readline` keeps trailing `\n`. `file.readlines` → e.g. `['Line 1\n', 'Line 2\n']`.
- `file.write` → int byte count; `file.writelines` requires list, returns `None`.
- `file.seek(handle, offset, [whence])`: `0=beg, 1=cur, 2=end`. `file.tell` → int. `file.eof` → `True`/`False`.
- `file.exists` → int `1`/`0` (both work in `if`: `0` falsy, `1` truthy).
- `file.remove` / `file.mkdir` (recursive `create_directories`) → `None`. `file.listdir` → list of filenames (not full paths).
- Wrong arity → e.g. `Error: file.open expects 1 or 2 arguments: filename, [mode]`.

Full demo (`all_syntax_demo.knx`):

```knx
let fw = call file.open("demo_output.txt", "w")
call file.write(fw, "Line 1: Hello Knox!\n")
call file.writelines(fw, ["Line 3: List write\n", "Line 4: Final line\n"])
call file.close(fw)

let fr = call file.open("demo_output.txt", "r")
print call file.read(fr)
call file.close(fr)

print call file.exists("demo_output.txt")   </> 1 <\>
call file.mkdir("demo_dir")
print call file.listdir("demo_dir")
call file.remove("demo_output.txt")
```

### 10.12 Modules — `use` / `take`

Semicolon `;` **required** after `use Mod;` and `take Mod use a, b;`.

```knx
use Maths;
take Maths use sub;
take Maths use add, sub;
```

- Resolver tries variants: `name`, `lower(name)`, `Capitalized`, `lower+.knx`, in each of `[<program dir>, "."]`. So `Maths`, `maths`, `MATHS` resolve case-insensitively to `Maths.knx`/`maths.knx`.
- `use` imports all funcs+vars, cached by lowercase (second `use` is no-op).
- `take` imports only listed names; missing → `Error: 'x' not found in module 'Mod'`.
- Missing module → `Error: Module 'X' not found in ['<progdir>', '.']`.
- Top-level stray `;` skipped; unexpected top-level tokens silently skipped; inside blocks every statement accepted.

### 10.13 Truthiness, Undefined Variables, Numbers

- Undefined variable read → `0` (mirrors `get(name, 0)`), not an error.
- Truthiness = Python: `None/False/0/0.0/""/[]` falsy, everything else truthy.
- `bool` promotes in arithmetic (`true`→1).
- No built-in `and`/`or`/`not` — use comparisons + `if`/`again if`/`else` + `true`/`false`.
- No built-in `sin`/`cos`/`sqrt`/`split`, maps/dicts, sets, tuples, higher-order functions, network/GUI — use `maths.knx` funcs or write your own.

---

## 11. Standard Library

**File Functions (14, always available, `call file.*`):**
`file.open`, `file.close`, `file.read`, `file.readline`, `file.readlines`, `file.write`, `file.writelines`, `file.seek`, `file.tell`, `file.eof`, `file.exists`, `file.remove`, `file.mkdir`, `file.listdir`

**I/O (4 forms):** `print`, `user`, `int.user`, `str.user`

**String methods:** `.upper .lower .len .strip .replace("old","new") .find("sub")` (+ `.len` on lists)

**`stdlib/maths.knx`** — BOARDMAS-compliant math library (use via `use maths;` with file next to your program):

```knx
</> maths.knx - constants + pure-arithmetic funcs <\>
let PI = 314
let E  = 271
let TAU = 628

func add:a b[ return a+b ]
func sub:a b[ return a-b ]
func mul:a b[ return a*b ]
func div:a b[ return a/b ]
func neg:a[ return 0-a ]
func inc:a[ return a+1 ]
func dec:a[ return a-1 ]
func double:a[ return a*2 ]
func triple:a[ return a*3 ]
func scale:a b[ return a*b ]
func square:a[ return a*a ]
func cube:a[ return a*a*a ]
func pow4:a[ return a*a*a*a ]
func pow5:a[ return a*a*a*a*a ]
func sum3:a b c[ return a+b+c ]
func sum4:a b c d[ return a+b+c+d ]
func product3:a b c[ return a*b*c ]
func product4:a b c d[ return a*b*c*d ]
func avg2:a b[ return (a + b) / 2 ]
func avg3:a b c[ return (a + b + c) / 3 ]
func avg4:a b c d[ return (a + b + c + d) / 4 ]
func halve:a[ return a/2 ]
func reciprocal:a[ return 1/a ]
func percent:a p[ return (a * p) / 100 ]
```

> Multi-arg averages MUST use brackets, e.g. `(a + b) / 2` — otherwise BOARDMAS gives wrong results.

Registered in C++ (`registerFileFunctions()` in `src/filefuncs.cpp`) + core `print`/`user` — no hidden extras. See `src/filefuncs.cpp` + `visitUserInput`/`visitTypedInput` for the full list.

---

## 12. Complete Examples

**Log processor (`all_syntax_demo.knx` excerpt):**

```knx
let log = call file.open("app.log", "w")
call file.write(log, "2024-01-01 10:00:00 INFO App started\n")
call file.write(log, "2024-01-01 10:00:10 ERROR Database connection failed\n")
call file.close(log)

let log_file = call file.open("app.log", "r")
print call file.read(log_file)
call file.close(log_file)
call file.remove("app.log")
```

**Built-ins demo:**

```knx
print "Welcome to Knox!"
let name = str.user("What is your name? ")
let age = int.user("How old are you? ")
let f = call file.open("data.txt", "w")
call file.write(f, "hello\n")
call file.close(f)
if call file.exists("config.txt")[
    print "Config file found."
]
```

Run everything after building:

```bash
./compiler-cpp/build/knox program.knx
knox all_syntax_demo.knx
knox test_file.knx
knox test_import.knx
printf '20\n' | knox program.knx
```

---

## 13. Testing

Each test is a `.knx` program with a checked-in `.expected` file (stdout). No Python needed.

```bash
cd compiler-cpp
./tests/run_tests.sh
```

Test groups: `arithmetic`, `floats`, `strings`, `booleans`, `variables`, `conditions`, `loops`, `functions`, `imports`, `stdlib` (files), `errors` (try/catch), `integration`, `input` (piped stdin in the runner).

Broader manual checks:

```bash
./build/knox ../all_syntax_demo.knx
./build/knox ../test_file.knx
./build/knox ../test_import.knx
```

Conformance (backend + examples + IDE + Codes):

```bash
./tests/test_cross_backend.sh   # must be 14/14 green
```

---

## 14. Architecture

```text
program.knx
  -> Lexer (include/lexer.hpp, src/lexer.cpp)
  -> Parser (include/parser.hpp, src/parser.cpp)
  -> AST (include/ast.hpp, src/ast.cpp)
  -> Interpreter + values + file.* + try/catch + imports
     (include/value.hpp + src/value.cpp,
      include/interpreter.hpp + src/interpreter.cpp + src/filefuncs.cpp)
  -> CLI (src/main.cpp), errors (include/errors.hpp, src/errors.cpp)
```

| Stage | Files |
|-------|-------|
| Tokens | `include/token.hpp`, `src/token.cpp` |
| Lexer | `include/lexer.hpp`, `src/lexer.cpp` |
| AST | `include/ast.hpp`, `src/ast.cpp` |
| Parser | `include/parser.hpp`, `src/parser.cpp` |
| Runtime values, interpreter, `file.*`, `try/catch`, imports | `include/value.hpp` + `src/value.cpp`, `include/interpreter.hpp` + `src/interpreter.cpp` + `src/filefuncs.cpp` |
| CLI | `src/main.cpp` |
| Errors | `include/errors.hpp`, `src/errors.cpp` |

Memory safety by RAII only: AST `std::unique_ptr`, functions `std::shared_ptr`, values `std::variant` + `std::shared_ptr<std::vector<Value>>`. No `new`/`delete`/`malloc`/`free`.

---

## 15. Troubleshooting & FAQ

| Symptom | Fix |
|---------|-----|
| `File not found: X` | Check path + `.knx` extension |
| `Error: ...` on stderr | Knox lex/parse/runtime error (`_error`-style message) — fix the `.knx` line it describes |
| `Module 'x' not found` | Put `x.knx` in program's dir; check case-insensitive match |
| `Error: 'x' not found in module 'Mod'` | `take` name must exist in module |
| `Error: Invalid file handle: N` | `file.open` failed or handle already closed |
| `Error: Unexpected token: TRY/CALL` | Space-form `call` swallowed next `try`/`call` line — use paren-form `call f(a,b)` |
| `❌ Invalid integer input` + `0` | `int.user` got non-integer — expected behavior |
| `Failed to start the Knox backend process` (IDE) | Set executable in Tools → Settings… |
| No input echo (IDE/Codes) | Type in Terminal/input row while running, press Send |
| `Unclosed comment block` | Missing `<\>` — comments cannot nest |
| `Invalid character: X` | Character outside Knox lexer alphabet |
| Run never finishes | Infinite loop — Stop the process, check `while`/`for` conditions |

**FAQ:**

- *Do I need Python?* No. Sole runtime is `compiler-cpp/build/knox` (C++20).
- *Where do imports resolve?* `[<program dir>, "."]`, case-insensitively.
- *Is `/` integer division?* No — `/` always returns float (`10/2 → 5.0`).
- *Are functions hoisted?* No — define before `call` (sequential execution).
- *Does `try` swallow `break`/`return`?* No — they propagate correctly.

---

## 16. Quick Reference / Cheat Sheet

```knx
</> comment <\>
let x = 42
let s = "hi"
let l = [1, 2, 3]
print x
print 10 + 5 * 2          </> 20 <\>
print (10 + 5) * 2        </> 30 <\>

let n = user("Name? ")
let a = int.user("Age? ")
let t = str.user("City? ")

if a >= 18 [
    print "adult"
] again if a >= 13 [
    print "teen"
] else [
    print "child"
]

let i = 0
while i < 3 [
    print i
    let i = i + 1
]
repeat 3 [ print "hi" ]
for k = 1 to 5 [ print k ]
</> break / ignore inside loops <\>

func add:a b[ return a + b ]
print call add(5, 3)
print call add 100 200

print s.upper
print s.lower
print s.len
print s.strip
print s.replace("a", "b")
print s.find("b")

try[ let r = 10 / 0 ] catch[ print _error ]

let f = call file.open("d.txt", "w")
call file.write(f, "hi\n")
call file.close(f)
print call file.exists("d.txt")

use Maths;
take Maths use add, sub;
```

| Category | Syntax |
|----------|--------|
| Comment | `</> text <\>` |
| Assignment | `let name = expr` |
| Block | `[ statements ]` |
| Print / input | `print expr`, `user("p")`, `int.user("p")`, `str.user("p")` |
| Conditionals | `if cond [...] again if cond [...] else [...]` |
| Loops | `while cond [...]`, `repeat n [...]`, `for i = a to b [...]`, `break`, `ignore` |
| Functions | `func n:p1 p2[ body ]`, `call n(a,b)`, `call n a b`, `return expr` |
| Strings | `s.upper s.lower s.len s.strip s.replace("o","n") s.find("s")` |
| Errors | `try[...] catch[...]` + `_error` |
| Files | `call file.open/close/read/readline/readlines/write/writelines/seek/tell/eof/exists/remove/mkdir/listdir(...)` |
| Modules | `use Mod;`, `take Mod use a, b;` |
| Run | `knox program.knx` |

---

## 17. Language Book Files

- [`KNOX_PROGRAMMING_LANGUAGE_BOOK.md`](KNOX_PROGRAMMING_LANGUAGE_BOOK.md) — **v2.0 C++ Edition** (source of truth): Ch. 0 (how to run `knox program.knx`, build, CLI contract, pipeline), Ch. 25 (built-in modules reference: 14× `file.*` + I/O, registration, call syntax), Appendix C++ (Python→C++ migration: BOARDMAS, unary minus, numbers, truthiness, functions, strings, comments, `use`/`take`, `try`/`catch`, checklist).
- [`Knox_Programming_Language_Book.md`](Knox_Programming_Language_Book.md) — **v1.0 original book**: full tutorial (Parts I–V, 27 chapters): intro, installation, syntax, variables, types, operators, conditionals, loops, functions, strings, lists, errors, files, modules, stdlib, best practices, debugging, reference, grammar, exercises, FAQ, glossary.
- [`compiler-cpp/README.md`](compiler-cpp/README.md) — backend architecture, behavior, build/run/testing, memory safety.
- [`docs/knox-ide.md`](docs/knox-ide.md) / [`docs/knox-codes.md`](docs/knox-codes.md) / [`docs/build-and-release.md`](docs/build-and-release.md) — IDE, Android app, release guide.

---

## 18. Version History

| Release | Backend | Notes |
|---------|---------|-------|
| Knox IDE 1.0.0 | 1.0.0 (`knox --version`) | Qt 6 desktop IDE |
| Knox Codes 1.0.0 | 1.0.0 (`KnoxBridge.backendVersion()`) | Android app (NDK, same C++ sources) |
| Book v1.0 | Python impl (`run.py`) | Original tutorial/reference (superseded) |
| Book v2.0 / Language 2.0 | C++20 (`compiler-cpp/build/knox`) | Sole runtime; no `*.py` remains |

Rule: never change Knox syntax/semantics in the apps. Backend changes flow `compiler-cpp → tests → apps` (rebuild IDE + Codes, re-run conformance).

---

## 19. License

```text
Copyright © 2026 Knox Programming Language Project

This book is the official reference manual for the Knox programming language.
It documents the language as implemented in the official Knox interpreter.

Permission is granted to copy, distribute, and/or modify this document
under the terms of the Creative Commons Attribution-ShareAlike 4.0
International License (CC BY-SA 4.0).

The Knox programming language implementation itself is released under
the MIT License.
```

---

*End of README — run any program with `knox program.knx` after building `compiler-cpp/build/knox`. See `KNOX_PROGRAMMING_LANGUAGE_BOOK.md` for the complete specification.*
