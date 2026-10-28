# SBCL & Common Lisp in 30 Days: A Course for Python Programmers

*Focus: machine learning, AI and visualization. Editor: Emacs + SLIME.*

## How this course works

**Audience:** you know Python well (functions, classes, NumPy/pandas basics, maybe PyTorch). You do not need prior Lisp or Emacs experience.

**Time:** about 1.5 to 2.5 hours per day for 30 days. Each day has a goal, the topics, a hands-on exercise, a "Python bridge" (how it maps to what you know) and further reading.

**Shape of the month:**

| Week | Days | Theme |
| --- | --- | --- |
| 1 | 1-7 | Tooling and core language |
| 2 | 8-14 | Real-world Lisp: packages, CLOS, macros, conditions, libraries, testing |
| 3 | 15-21 | Numerics, data, and machine learning |
| 4 | 22-30 | Symbolic AI, search, RL, visualization, LLM tooling, capstone |

**An honest note on the ecosystem.** Common Lisp is superb for interactive development, symbolic AI, DSLs and fast compiled code. Its numerical and deep-learning ecosystem is much smaller than Python's. This course therefore teaches you to (a) build ML from first principles in Lisp, (b) use the Lisp libraries that exist (Lisp-Stat, MAGICL, NUMCL, MGL, cl-waffe2), and (c) call Python from Lisp when that is the pragmatic choice.

**A note on links.** The URLs below are long-standing project and documentation pages, but the web changes. If one has moved, search the title, or use [CLiki](https://www.cliki.net/) and [Quickdocs](https://quickdocs.org/) to find libraries.

## Core references (keep these open all month)

- [Practical Common Lisp](https://gigamonkeys.com/book/) by Peter Seibel (free online). Main textbook.
- [The Common Lisp Cookbook](https://lispcookbook.github.io/cl-cookbook/). Task-oriented recipes.
- [SBCL Manual](https://www.sbcl.org/manual/)
- [Common Lisp HyperSpec](https://www.lispworks.com/documentation/HyperSpec/Front/) (the language standard, searchable)
- [SLIME manual](https://slime.common-lisp.dev/doc/html/)
- [Common Lisp: A Gentle Introduction to Symbolic Computation](https://www.cs.cmu.edu/~dst/LispBook/) (free PDF, gentler than PCL)
- [Paradigms of AI Programming (PAIP)](https://github.com/norvig/paip-lisp) by Peter Norvig (free on GitHub). The AI classic, used in Week 4.
- [Quicklisp](https://www.quicklisp.org/beta/): the library manager (like pip).

---

# WEEK 1: Tooling and the Core Language

## Day 1: Install SBCL, Quicklisp, Emacs and SLIME

**Goal:** a working REPL inside Emacs.

**Learn**

- What SBCL is: a fast native-code compiler implementing ANSI Common Lisp. Unlike CPython, every function you type at the REPL is compiled to machine code.
- Install SBCL (`brew install sbcl`, `apt install sbcl`, or the installer from sbcl.org).
- Install Quicklisp: download `quicklisp.lisp`, run `sbcl --load quicklisp.lisp`, then `(quicklisp-quickstart:install)` and `(ql:add-to-init-file)`.
- Install SLIME through Quicklisp: `(ql:quickload :quicklisp-slime-helper)`, then add the snippet it prints to your Emacs `init.el` (set `inferior-lisp-program` to `"sbcl"`).
- Start with `M-x slime`.

**Practice:** at the REPL evaluate `(+ 1 2)`, `(format t "Hello, ~a!~%" "Lisp")` and `(ql:quickload :alexandria)`.

**Python bridge:** SBCL's REPL is like IPython, but your whole program lives in one long-running image that you modify incrementally. Quicklisp \~ pip; ASDF systems \~ packages.

**Fallback:** if Emacs setup fights you, install [Portacle](https://portacle.github.io/), a portable bundle of Emacs + SBCL + SLIME + Quicklisp.

**Read**

- [Practical Common Lisp, ch. 2: Lather, Rinse, Repeat (setup)](https://gigamonkeys.com/book/lather-rinse-repeat-a-tour-of-the-repl.html)
- [Cookbook: Getting started](https://lispcookbook.github.io/cl-cookbook/getting-started.html)
- [Cookbook: Emacs as a Lisp IDE](https://lispcookbook.github.io/cl-cookbook/emacs-ide.html)
- [Quicklisp beta page](https://www.quicklisp.org/beta/)

## Day 2: Emacs survival kit for Lisp

**Goal:** edit Lisp structurally and fluently.

**Learn**

- Emacs basics: run the built-in tutorial (`C-h t`), buffers, windows, `M-x`, `C-g`.
- Install **Paredit** (or Lispy) and **rainbow-delimiters** via `M-x package-install`. Parentheses stop being a problem once the editor balances them.
- SLIME essentials:
  - `C-x C-e` eval the expression before the cursor
  - `C-M-x` or `C-c C-c` compile the top-level form
  - `C-c C-k` compile and load the whole file
  - `M-.` jump to definition, `M-,` jump back
  - `C-c C-d d` describe symbol, `C-c C-d h` open HyperSpec entry
  - `C-c C-z` switch to REPL, `C-c I` inspect an object
- Paredit: `C-)` slurp, `C-(` barf, `M-s` splice, `M-(` wrap.

**Practice:** create `day02.lisp`, write three small functions, evaluate each one with `C-c C-c`, and call them from the REPL.

**Python bridge:** you'll stop running `python file.py` over and over. You send individual definitions into a live image, like a notebook that is also your editor.

**Read**

- [SLIME manual](https://slime.common-lisp.dev/doc/html/)
- [Paredit cheat sheet](https://www.emacswiki.org/emacs/PareditCheatsheet)
- [Lispy (alternative to Paredit)](https://github.com/abo-abo/lispy)
- [SLY (a fork of SLIME, optional)](https://github.com/joaotavora/sly)
- [Cookbook: Emacs IDE page](https://lispcookbook.github.io/cl-cookbook/emacs-ide.html)

## Day 3: Syntax, atoms and variables

**Goal:** read and write S-expressions confidently.

**Learn**

- Everything is `(operator arg1 arg2 ...)`. Code is data.
- Numbers: integers are arbitrary precision, ratios (`1/3`), `single-float`, `double-float` (write `1.0d0`), complex.
- Strings, characters (`#\a`), symbols, keywords (`:foo`), `NIL` (false and the empty list), `T`.
- Variables: `defvar`/`defparameter` (global, special), `let`/`let*` (local), `setf`.
- Quote: `'x` vs `x`; evaluation rules.
- Printing: `format` directives (`~a`, `~s`, `~d`, `~f`, `~%`).

**Practice:** write a temperature-conversion function using `format`; experiment with `(/ 1 3)` vs `(/ 1.0 3)`.

**Python bridge:** `None`/`False`/`[]` collapse into one `NIL`. There is no operator precedence, because everything is prefix. Case-insensitive symbols are upcased by the reader.

**Read**

- [PCL ch. 3? No: ch. 4 Syntax and Semantics](https://gigamonkeys.com/book/syntax-and-semantics.html)
- [PCL ch. 10: Numbers, Characters and Strings](https://gigamonkeys.com/book/numbers-characters-and-strings.html)
- [Cookbook: Data structures overview](https://lispcookbook.github.io/cl-cookbook/data-structures.html)
- [HyperSpec: Format directives](https://www.lispworks.com/documentation/HyperSpec/Body/22_c.htm)

## Day 4: Functions

**Goal:** define flexible functions and use functions as values.

**Learn**

- `defun`, docstrings, lambda lists: `&optional`, `&key`, `&rest`.
- `lambda`, `funcall`, `apply`, `#'function`. Lisp-2: functions and variables live in separate namespaces.
- Closures and lexical scope.
- Multiple return values: `values`, `multiple-value-bind`.
- Higher-order basics: `mapcar`, `reduce`, `remove-if`.

**Practice:** write `(make-counter)` that returns a closure; write a `(compose f g)`; write a function with `&key` arguments that mimics a Python function with default and keyword parameters.

**Python bridge:** `&key` \~ keyword args; `&rest` \~ `*args`; `&optional` \~ default positional args; `values` \~ returning a tuple (but cheaper and optional to unpack).

**Read**

- [PCL ch. 5: Functions](https://gigamonkeys.com/book/functions.html)
- [Cookbook: Functions](https://lispcookbook.github.io/cl-cookbook/functions.html)
- [Gentle Introduction (CMU)](https://www.cs.cmu.edu/~dst/LispBook/)

## Day 5: Conses and lists

**Goal:** understand the data structure Lisp is named after.

**Learn**

- `cons`, `car`/`first`, `cdr`/`rest`, `list`, `append`, `nth`, `length`, `reverse`.
- Lists as linked chains of cons cells; dotted pairs; structure sharing.
- Sequence functions that work on lists, vectors and strings: `subseq`, `find`, `position`, `remove-if`, `sort` (destructive!), `reduce`, `every`, `some`.
- Destructuring: `destructuring-bind`.
- Association lists and property lists.

**Practice:** implement `my-map`, `my-filter`, `my-reverse` recursively, then compare with built-ins. Write a function that flattens nested lists.

**Python bridge:** Lisp lists are linked lists, not dynamic arrays. Use vectors when you want O(1) random access (like Python lists). `sort` mutates; use `(sort (copy-seq x) #'<)`.

**Read**

- [PCL ch. 12: They Called It LISP for a Reason: List Processing](https://gigamonkeys.com/book/they-called-it-lisp-for-a-reason-list-processing.html)
- [PCL ch. 11: Collections](https://gigamonkeys.com/book/collections.html)
- [Cookbook: Lists](https://lispcookbook.github.io/cl-cookbook/data-structures.html#lists)

## Day 6: Control flow and iteration

**Goal:** write loops and conditionals idiomatically.

**Learn**

- `if`, `when`, `unless`, `cond`, `case`/`ecase`, `and`/`or`.
- Iteration: `dolist`, `dotimes`, `do`, and the powerful `loop` macro (`for ... in`, `collect`, `sum`, `when`, `finally`).
- Recursion and tail calls (SBCL does optimize tail calls in compiled code at normal optimization settings, but don't rely on it for portability).

**Practice:** rewrite five Python list comprehensions using `loop` or `mapcar`; solve FizzBuzz and prime sieve with `loop`.

**Python bridge:** `(loop for x in xs when (evenp x) collect (* x x))` \~ `[x*x for x in xs if x % 2 == 0]`.

**Read**

- [PCL ch. 7: Macros: Standard Control Constructs](https://gigamonkeys.com/book/macros-standard-control-constructs.html)
- [PCL ch. 22: LOOP for Black Belts](https://gigamonkeys.com/book/loop-for-black-belts.html)
- [Cookbook: Loop, iteration, mapping](https://lispcookbook.github.io/cl-cookbook/iteration.html)

## Day 7: Data structures and a first mini-project

**Goal:** use arrays, hash tables and structs; ship a small program.

**Learn**

- Vectors/arrays: `make-array`, `aref`, `vector-push-extend`, adjustable arrays, multi-dimensional arrays.
- Hash tables: `make-hash-table :test 'equal`, `gethash`, `maphash`, `loop ... being the hash-keys`.
- `defstruct`: fast, simple records with accessors.
- Reading files: `with-open-file`, `read-line`.

**Mini-project:** word-frequency counter. Read a text file, count words in a hash table, print the top 10 sorted.

**Python bridge:** hash table \~ `dict` (but pick `:test 'equal` for string keys); `defstruct` \~ `dataclass`; multi-dim array \~ NumPy `ndarray` (without vectorized operators).

**Read**

- [PCL ch. 13: Beyond Lists: Other Uses for Cons Cells](https://gigamonkeys.com/book/beyond-lists-other-uses-for-cons-cells.html)
- [PCL ch. 14: Files and File I/O](https://gigamonkeys.com/book/files-and-file-io.html)
- [Cookbook: Files and directories](https://lispcookbook.github.io/cl-cookbook/files.html)
- [Cookbook: Arrays, hash tables](https://lispcookbook.github.io/cl-cookbook/data-structures.html)

---

# WEEK 2: Real-World Lisp

## Day 8: Packages and ASDF systems

**Goal:** structure a project the way the community does.

**Learn**

- `defpackage`, `in-package`, `:use`, `:export`, package-qualified symbols (`str:join`).
- ASDF system definitions (`.asd`), `:depends-on`, `:components`.
- Put projects in `~/quicklisp/local-projects/` (or `~/common-lisp/`) so `(ql:quickload :myproj)` finds them.
- Scaffolding: [cl-project](https://github.com/fukamachi/cl-project) or [cl-skeleton generators](https://www.cliki.net/project%20skeleton).

**Practice:** turn your Day 7 word counter into a proper system with `src/`, `tests/` and an `.asd` file.

**Python bridge:** package \~ module namespace; `.asd` \~ `pyproject.toml`; `ql:quickload` \~ `pip install` + `import` in one step.

**Read**

- [PCL ch. 21: Programming in the Large: Packages and Symbols](https://gigamonkeys.com/book/programming-in-the-large-packages-and-symbols.html)
- [ASDF manual](https://asdf.common-lisp.dev/asdf.html)
- [Cookbook: Packages](https://lispcookbook.github.io/cl-cookbook/packages.html)
- [Cookbook: Libraries and Quicklisp](https://lispcookbook.github.io/cl-cookbook/getting-started.html#how-to-use-libraries)

## Day 9: CLOS, the Common Lisp Object System

**Goal:** object-oriented programming, Lisp style.

**Learn**

- `defclass` with `:initarg`, `:accessor`, `:initform`; `make-instance`.
- Generic functions and methods: `defgeneric`, `defmethod`. Methods belong to generic functions, not classes.
- **Multiple dispatch** on several arguments.
- Inheritance, `call-next-method`, `:before`/`:after`/`:around` methods.
- `print-object` (like `__repr__`), `initialize-instance`.

**Practice:** model a small `layer` class hierarchy (`dense-layer`, `relu-layer`) with a generic `forward` function. This prefigures the neural network you'll build on Day 20.

**Python bridge:** no `self.method()` syntax, you write `(forward layer x)`. Multiple dispatch \~ what `functools.singledispatch` approximates for one argument.

**Read**

- [PCL ch. 16: Object Reorientation: Generic Functions](https://gigamonkeys.com/book/object-reorientation-generic-functions.html)
- [PCL ch. 17: Object Reorientation: Classes](https://gigamonkeys.com/book/object-reorientation-classes.html)
- [Cookbook: CLOS](https://lispcookbook.github.io/cl-cookbook/clos.html)

## Day 10: The condition system and debugging

**Goal:** use Lisp's powerful error handling and the SLIME debugger.

**Learn**

- `handler-case` (like `try/except`), `handler-bind`, `error`, `warn`, `signal`, custom condition classes with `define-condition`.
- **Restarts**: `restart-case`, `invoke-restart`. Error handling that lets the caller decide how to continue, without unwinding.
- `unwind-protect` (like `finally`), `ignore-errors`.
- Debugging: the SLIME debugger (inspect frames, edit code, restart the frame without losing state), `trace`, `break`, `step`, `inspect`, `describe`.

**Practice:** write a CSV-line parser that signals a `malformed-row` condition and offer restarts `skip-row` and `use-default`.

**Python bridge:** in Python an exception destroys the stack. In Lisp the stack stays alive in the debugger, so you can fix the bug, recompile and resume.

**Read**

- [PCL ch. 19: Beyond Exception Handling: Conditions and Restarts](https://gigamonkeys.com/book/beyond-exception-handling-conditions-and-restarts.html)
- [PCL ch. 18? see ch. 18 A Few FORMAT Recipes](https://gigamonkeys.com/book/a-few-format-recipes.html)
- [Cookbook: Error and condition handling](https://lispcookbook.github.io/cl-cookbook/error_handling.html)
- [Cookbook: Debugging](https://lispcookbook.github.io/cl-cookbook/debugging.html)

## Day 11: Macros, part 1

**Goal:** write code that writes code.

**Learn**

- Why macros: new syntax without runtime cost; they run at compile time and transform S-expressions.
- Backquote/unquote: `` ` `` , `,` , `,@`.
- `defmacro`, `macroexpand-1` (use `C-c C-m` in SLIME), variable capture and `gensym`.
- Write `with-timing`, `unless-zero`, a `while` loop and an `aif`.

**Practice:** implement `(my-while cond body...)` and `(with-timing (label) body...)` that prints elapsed time.

**Python bridge:** closest Python analogues are decorators, context managers and AST rewriting, but macros are far simpler and more integrated.

**Read**

- [PCL ch. 8: Macros: Defining Your Own](https://gigamonkeys.com/book/macros-defining-your-own.html)
- [Cookbook: Macros](https://lispcookbook.github.io/cl-cookbook/macros.html)
- [On Lisp by Paul Graham (free)](https://paulgraham.com/onlisp.html)

## Day 12: Macros, part 2 and functional style

**Goal:** use macros wisely and write functional-style Lisp.

**Learn**

- When *not* to use a macro (prefer functions; macros can't be passed to `mapcar`).
- Macro-writing patterns: `with-...` resource macros, `do-...` iteration macros, `defmacro` that generates `defun`s.
- Functional tools: `reduce` with `:initial-value`, `mapcan`, `remove-duplicates`, `funcall` pipelines.
- The [Iterate](https://common-lisp.net/project/iterate/) library as a more extensible alternative to `loop`.
- Threading-style macros (`arrows` library `->` and `->>`), similar to pipelines in other languages.

**Practice:** write a `defstruct`-like macro `def-record` that generates a class, constructor and printer. Then replace a nested function call with `->>`.

**Python bridge:** `->>` resembles method chaining in pandas. Macros let you build such syntax yourself.

**Read**

- [PCL ch. 9: Practical: Building a Unit Test Framework (a real macro project)](https://gigamonkeys.com/book/practical-building-a-unit-test-framework.html)
- [PCL ch. 10 (Numbers) and ch. 6: Variables](https://gigamonkeys.com/book/variables.html)
- [Arrows library](https://github.com/Harleqin/arrows)
- [Iterate manual](https://iterate.common-lisp.dev/)

## Day 13: The standard library of the community

**Goal:** know the "batteries" you'll use constantly.

**Learn** (install each with `ql:quickload`)

- [alexandria](https://alexandria.common-lisp.dev/): utilities (`when-let`, `flatten`, `hash-table-keys`, `iota`).
- [serapeum](https://github.com/ruricolist/serapeum): modern utilities.
- [cl-ppcre](https://edicl.github.io/cl-ppcre/): regular expressions.
- [str](https://github.com/vindarel/cl-str): string manipulation.
- [local-time](https://github.com/dlowe-net/local-time): dates and times.
- [jzon](https://github.com/Zulu-Inuoe/jzon) or [yason](https://github.com/phmarek/yason): JSON.
- [cl-csv](https://github.com/AccelerationNet/cl-csv): CSV.
- [dexador](https://github.com/fukamachi/dexador): HTTP client (like `requests`).
- [uiop](https://asdf.common-lisp.dev/uiop.html): OS utilities (comes with ASDF).

**Practice:** fetch a JSON API with dexador, parse it with jzon, save selected fields to CSV with cl-csv.

**Python bridge:** dexador \~ `requests`; jzon \~ `json`; cl-ppcre \~ `re`; local-time \~ `datetime`.

**Read**

- [Awesome CL (curated library list)](https://github.com/CodeCommunity/awesome-cl)
- [Cookbook: Strings, Regular expressions, Dates, Web](https://lispcookbook.github.io/cl-cookbook/)
- [Quickdocs: search any library](https://quickdocs.org/)

## Day 14: Testing, types and performance

**Goal:** write tests and make Lisp code fast, a key advantage over Python.

**Learn**

- Testing with [FiveAM](https://github.com/lispci/fiveam): `def-suite`, `test`, `is`, `run!`.
- Gradual typing: `(declaim (ftype (function (double-float) double-float) f))`, `(declare (type fixnum i))`.
- Optimization knobs: `(declaim (optimize (speed 3) (safety 1) (debug 0)))`; SBCL prints optimization notes that tell you why code is slow.
- Measuring: `time`, the statistical profiler `sb-sprof`, `disassemble`.
- Compare a naive loop with a type-declared one on summing a million doubles.

**Mini-project (end of Week 2):** a CSV loader with condition-based error handling, FiveAM tests and a benchmark.

**Python bridge:** this is where you notice that Lisp code with type declarations can approach C speeds, without leaving the language (no Cython/Numba needed).

**Read**

- [SBCL manual: Compiler / Efficiency chapters](https://www.sbcl.org/manual/)
- [Cookbook: Testing](https://lispcookbook.github.io/cl-cookbook/testing.html)
- [Cookbook: Performance and type declarations](https://lispcookbook.github.io/cl-cookbook/performance.html)
- [PCL ch. 15? see ch. 24 Practical: Parsing Binary Files (optional, advanced)](https://gigamonkeys.com/book/practical-parsing-binary-files.html)

---

# WEEK 3: Numerics, Data and Machine Learning

## Day 15: Numeric arrays and speed

**Goal:** build your own mini-NumPy and understand why typed arrays are fast.

**Learn**

- Specialized arrays: `(make-array '(3 3) :element-type 'double-float :initial-element 0d0)`.
- `row-major-aref`, `array-dimensions`, displaced arrays and fill pointers.
- `(simple-array double-float (* *))` declarations and `(declare (optimize speed))`.
- Float traps and `sb-int:with-float-traps-masked` for NaN/infinity behavior.

**Practice:** implement matrix multiplication three ways: naive and untyped, typed with declarations, and blocked. Benchmark against NumPy (`@`) via `time`. Expect the typed version to be far faster than untyped and slower than BLAS-backed NumPy, which makes the point about when to reach for a BLAS binding.

**Python bridge:** NumPy arrays \~ specialized Lisp arrays; broadcasting is not built in (libraries add it).

**Read**

- [SBCL manual: Arrays, Declarations](https://www.sbcl.org/manual/)
- [Cookbook: Arrays](https://lispcookbook.github.io/cl-cookbook/data-structures.html#arrays)
- [HyperSpec: Arrays dictionary](https://www.lispworks.com/documentation/HyperSpec/Body/c_arrays.htm)

## Day 16: Numerical libraries: NUMCL, MAGICL, LLA

**Goal:** get NumPy-like operations in Lisp.

**Learn**

- [NUMCL](https://github.com/numcl/numcl): a NumPy clone (broadcasting, `numcl:+`, reshape, einsum-like ops).
- [MAGICL](https://github.com/quil-lang/magicl): matrix algebra backed by BLAS/LAPACK (SVD, eigendecomposition, solve). Install system BLAS/LAPACK first.
- [LLA](https://github.com/Lisp-Stat/lla): linear algebra via LAPACK.
- [array-operations](https://github.com/Lisp-Stat/array-operations) (aops): reshape, stack, slice utilities.

**Practice:** solve a linear system, compute an SVD, and fit a polynomial by least squares. Check results against NumPy.

**Python bridge:** expect fewer tutorials and rougher edges than NumPy; read the README and tests as documentation.

**Read**

- [NUMCL README and examples](https://github.com/numcl/numcl)
- [MAGICL docs](https://github.com/quil-lang/magicl)
- [Quickdocs: numcl](https://quickdocs.org/numcl)

## Day 17: Lisp-Stat: data frames and statistics

**Goal:** do pandas-style data work in Lisp.

**Learn**

- [Lisp-Stat](https://lisp-stat.dev/): an environment for statistical computing (data frames, distributions, regression, plotting).
- Load a CSV into a data frame, filter, select columns, compute summary statistics, and fit a simple linear model.
- Built-in sample datasets (e.g., mtcars-like data) for quick experiments.

**Practice:** load a public CSV (for example Iris) and compute per-class means and standard deviations. Then make a scatter plot (preview of Day 26).

**Python bridge:** `data-frame` \~ pandas `DataFrame`; the API is smaller and more R-flavored.

**Read**

- [Lisp-Stat documentation](https://lisp-stat.dev/)
- [Lisp-Stat on GitHub](https://github.com/Lisp-Stat)
- [Lisp-Stat resources/tutorials](https://lisp-stat.dev/resources/)

## Day 18: Bridging to Python and C

**Goal:** use Python's ML ecosystem from Lisp when it makes sense.

**Learn**

- [py4cl2](https://github.com/digikar99/py4cl2): call Python (NumPy, scikit-learn, PyTorch, matplotlib) from SBCL; exchange arrays and objects.
- [CFFI](https://cffi.common-lisp.dev/): call C libraries directly (`defcfun`, `with-foreign-object`).
- Decision rule: prototype in Lisp, reach for a Python or C library for heavyweight training.

**Practice:** train a scikit-learn logistic regression on Iris from the Lisp REPL via py4cl2 and compare it with your own version tomorrow.

**Python bridge:** you are not abandoning Python. A common real-world pattern is a Lisp front end orchestrating Python kernels.

**Read**

- [py4cl2 README](https://github.com/digikar99/py4cl2)
- [CFFI user manual](https://cffi.common-lisp.dev/manual/index.html)
- [Cookbook: Foreign Function Interfaces](https://lispcookbook.github.io/cl-cookbook/ffi.html)

## Day 19: Linear and logistic regression from scratch

**Goal:** implement gradient-descent learning with typed arrays.

**Learn / build**

- Represent data as `(simple-array double-float (* *))`.
- Write `dot`, `mat-vec`, a loss function (MSE, cross-entropy), a gradient function and a training loop.
- Mini-batches, learning-rate schedules, and early stopping using `loop`.
- Verify against scikit-learn from Day 18.

**Practice:** implement logistic regression on Iris (setosa vs non-setosa). Print loss every 50 epochs and plot it next week.

**Python bridge:** your first end-to-end ML model in Lisp. Notice how interactive redefinition of `step` or `loss` while training runs speeds experimentation.

**Read**

- [CS229 lecture notes: supervised learning (Stanford)](https://cs229.stanford.edu/notes2022fall/main_notes.pdf)
- [Gradient descent chapter (Dive into Deep Learning)](https://d2l.ai/chapter_linear-regression/index.html)
- [Cookbook: Performance](https://lispcookbook.github.io/cl-cookbook/performance.html)

## Day 20: Autodiff and a neural network from scratch

**Goal:** build a tiny micrograd-style engine and train an MLP.

**Learn / build**

- A `value` class (CLOS) storing data, gradient, children and a backward closure.
- Operators `add`, `mul`, `tanh`/`relu`, topological sort and `backward`.
- A `layer`/`mlp` hierarchy using the CLOS design from Day 9.
- Train on XOR, then on a two-moons toy dataset.

**Practice:** replicate Karpathy's micrograd in Lisp (about 150 lines). Closures and generic functions make this elegant.

**Python bridge:** the same algorithm as PyTorch's autograd, in a language where you can inspect and redefine every object live.

**Read**

- [micrograd (Andrej Karpathy)](https://github.com/karpathy/micrograd)
- [Neural Networks: Zero to Hero (video series)](https://karpathy.ai/zero-to-hero.html)
- [Neural Networks and Deep Learning (free book, Nielsen)](http://neuralnetworksanddeeplearning.com/)

## Day 21: Lisp ML libraries and Week 3 review

**Goal:** survey what exists and pick a stack.

**Learn**

- [MGL](https://github.com/melisgl/mgl) (Gábor Melis): neural nets, Boltzmann machines, gradient-based training, used in competitions. Read the docs and examples; the API is dense.
- [cl-waffe2](https://github.com/hikettei/cl-waffe2): a deep-learning framework for CL with a lazy computation graph and JIT-style compilation.
- [Lisp-Stat](https://lisp-stat.dev/) for classical statistics and [cl-libsvm](https://www.cliki.net/cl-libsvm)-style wrappers; browse [CLiki's machine learning page](https://www.cliki.net/machine%20learning).
- Status check: some of these libraries are lightly maintained, so verify that the one you choose loads on your SBCL version.

**Practice:** run an example from MGL or cl-waffe2 (for instance a small classifier), then compare how long it takes against your Day 20 network.

**Review checklist:** typed arrays, NUMCL/MAGICL, Lisp-Stat data frames, py4cl2, hand-written gradient descent and autodiff.

**Read**

- [MGL documentation](https://melisgl.github.io/mgl-pax-world/mgl-manual.html)
- [cl-waffe2 docs](https://hikettei.github.io/cl-waffe2/)
- [CLiki: Machine learning](https://www.cliki.net/machine%20learning)
- [Gábor Melis's blog](https://quotenil.com/)

---

# WEEK 4: Symbolic AI, Visualization, LLM Tooling and Capstone

## Day 22: Symbolic AI and pattern matching (PAIP)

**Goal:** use the area where Lisp historically shines.

**Learn**

- Symbols as data, `eq`/`eql`/`equal`, `assoc`, `sublis`.
- Norvig's pattern matcher with variables (`?x`), segment matching, and rule-based responses (ELIZA).
- Simple symbolic algebra: a derivative and simplifier for expressions like `(+ (* 3 x) 5)`.

**Practice:** build a small ELIZA-style chatbot, then a symbolic differentiator that you can later check against your autodiff from Day 20.

**Python bridge:** code-as-data makes expression trees trivial; no parser or AST library is needed.

**Read**

- [PAIP: Chapter 5 (ELIZA) and 8 (symbolic math)](https://github.com/norvig/paip-lisp)
- [PAIP in HTML form (online reading)](https://norvig.github.io/paip-lisp/)
- [Structure and Interpretation of Computer Programs (free)](https://mitpress.mit.edu/9780262510875/structure-and-interpretation-of-computer-programs/)

## Day 23: Search and planning

**Goal:** implement classic AI search algorithms.

**Learn**

- Generic search with a problem protocol (CLOS generics: `successors`, `goal-p`, `cost`).
- Breadth-first, depth-first, uniform cost, greedy best-first and **A\***.
- Priority queues (a simple heap or the [cl-heap](https://www.cliki.net/cl-heap) / [priority-queue](https://www.cliki.net/priority-queue) libraries).
- Heuristics: Manhattan distance for grid pathfinding.

**Practice:** solve a maze and the 8-puzzle with A\*. Count expanded nodes for each algorithm.

**Python bridge:** CLOS multiple dispatch lets you swap problem types and strategies without class hierarchies getting tangled.

**Read**

- [PAIP: Chapter 6 (GPS) and Chapter 7? see Chapter 6 "Building Software Tools" and Chapter 8 for search material](https://norvig.github.io/paip-lisp/)
- [Red Blob Games: Introduction to A\*](https://www.redblobgames.com/pathfinding/a-star/introduction.html)
- [AIMA code repository (Lisp version)](https://github.com/aimacode/aima-lisp)

## Day 24: Classical ML from scratch: k-means, k-NN, decision trees

**Goal:** implement three algorithms and compare them with scikit-learn.

**Learn / build**

- k-means with random restarts; distance functions on typed vectors.
- k-nearest neighbors with a priority queue.
- A decision tree (entropy or Gini) using `defstruct` nodes and recursion, a naturally recursive Lisp problem.

**Practice:** run all three on Iris; compare accuracy with scikit-learn through py4cl2.

**Python bridge:** recursion on trees is simple and fast with cons cells and structs.

**Read**

- [scikit-learn user guide: clustering and trees (for reference behavior)](https://scikit-learn.org/stable/user_guide.html)
- [Hands-on ML visual intuition: Decision trees (StatQuest, YouTube channel)](https://www.youtube.com/@statquest)
- [Cookbook: Structures and Recursion examples](https://lispcookbook.github.io/cl-cookbook/data-structures.html)

## Day 25: Reinforcement learning and evolutionary algorithms

**Goal:** build learning agents that don't need labeled data.

**Learn / build**

- Gridworld environment as a CLOS class (`reset`, `step`).
- Tabular Q-learning with epsilon-greedy exploration; hash table keyed by `(state action)` using `equal`.
- Genetic algorithm to solve a small optimization problem (for example, a knapsack problem or evolving a target string).

**Practice:** train a Q-learning agent on a 5x5 gridworld and log average reward per episode for plotting tomorrow.

**Python bridge:** the same idea as OpenAI Gym environments; your class protocol mirrors `reset()` / `step(action)`.

**Read**

- [Sutton & Barto, Reinforcement Learning: An Introduction (free)](http://incompleteideas.net/book/the-book-2nd.html)
- [Gymnasium environment API (for the protocol)](https://gymnasium.farama.org/)
- [Field Guide to Genetic Programming (free PDF)](http://www.gp-field-guide.org.uk/)

## Day 26: Visualization I: plots from the REPL

**Goal:** plot data from Lisp.

**Learn**

- **Lisp-Stat plot** (Vega-Lite): declarative charts from data frames; they open in the browser and are shareable as specs.
- [vgplot](https://github.com/volkers/vgplot) and [eazy-gnuplot](https://github.com/guicho271828/eazy-gnuplot): gnuplot front ends (install `gnuplot` on your system).
- Generating SVG directly (the format is just text): a hand-written scatter plot is a nice exercise in string/`format` skills.
- Alternative: use py4cl2 to call matplotlib (`py4cl2:import-module "matplotlib.pyplot"`) when you need a specific plot type.

**Practice:** plot your Day 19 and Day 20 loss curves, the Iris scatter colored by k-means cluster, and the Day 25 reward curve.

**Python bridge:** Vega-Lite specs are the same JSON that Altair produces, so your Python visualization knowledge transfers directly.

**Read**

- [Lisp-Stat plotting docs](https://lisp-stat.dev/docs/tasks/plotting/)
- [Vega-Lite documentation](https://vega.github.io/vega-lite/docs/)
- [vgplot README](https://github.com/volkers/vgplot)
- [Gnuplot documentation](http://www.gnuplot.info/documentation.html)

## Day 27: Visualization II: interactive dashboards on the web

**Goal:** serve a live dashboard from your Lisp image.

**Learn**

- [CLOG](https://github.com/rabbibotton/clog): build browser GUIs in Lisp; manipulate the DOM from your REPL.
- Or classic web stack: [Hunchentoot](https://edicl.github.io/hunchentoot/) (server) + [Spinneret](https://github.com/ruricolist/spinneret) or [cl-who](https://edicl.github.io/cl-who/) (HTML) + Chart.js/Plotly.js/Vega-Embed loaded from a CDN.
- Routes that return JSON; a page that polls it and redraws a chart.
- Because the image is live, you can redefine a handler and refresh the browser without restarting.

**Practice:** a dashboard that shows live training loss from a background training thread (use [bordeaux-threads](https://github.com/sionescu/bordeaux-threads)) with start/stop buttons.

**Python bridge:** this replaces Streamlit/Dash/Flask for many small tools, with hot-patching at the REPL.

**Read**

- [CLOG tutorials and manual](https://github.com/rabbibotton/clog/blob/main/docs/clog-manual.md)
- [Cookbook: Web development](https://lispcookbook.github.io/cl-cookbook/web.html)
- [Cookbook: Threads, concurrency](https://lispcookbook.github.io/cl-cookbook/process.html)
- [Plotly.js docs](https://plotly.com/javascript/)

## Day 28: LLM and agent tooling in Lisp

**Goal:** call a large language model API and build a tiny tool-using agent.

**Learn**

- HTTP + JSON with dexador and jzon: POST to an LLM API (for example, the Anthropic Messages API), with your API key read from an environment variable via `(uiop:getenv ...)`.
- Represent conversations as lists of plists/alists and build the request body with `jzon:stringify`.
- Tool use loop: define tools as Lisp functions in a hash table; send tool schemas; when the model asks for a tool call, `funcall` it and return the result.
- Why Lisp suits agents: the REPL lets you inspect each message and hot-patch tools while the agent runs; code-as-data helps with dynamic tool definitions.

**Practice:** build a REPL assistant with two tools (`read-file`, `run-calculation`) and a loop that continues until the model returns a final answer. Log every message to a file.

**Python bridge:** this is the same flow as using the Python SDK, just with plain HTTP and about 100 lines of Lisp.

**Read**

- [Anthropic API documentation](https://docs.claude.com/)
- [Tool use overview](https://docs.claude.com/en/docs/agents-and-tools/tool-use/overview)
- [dexador README](https://github.com/fukamachi/dexador)
- [jzon README](https://github.com/Zulu-Inuoe/jzon)

## Day 29: Capstone, part 1: design and build

**Goal:** combine the month into one project. Pick one:

1. **Iris/MNIST-style classifier lab:** data loading (Lisp-Stat), your own MLP (Day 20), a baseline from scikit-learn via py4cl2, and a web dashboard (CLOG) with training curves and a confusion matrix.
2. **RL playground:** a gridworld with Q-learning, live visualization of the policy as a heat map, adjustable hyperparameters in the browser.
3. **Symbolic + neural hybrid:** a symbolic expression simplifier plus an LLM-assisted helper that explains each step, with tests.

**Do today**

- Create an ASDF system (Day 8) with `src/`, `tests/`, and a README.
- Write the data layer and the core algorithm with FiveAM tests (Day 14).
- Add `declaim` type declarations to the hot loops and profile with `sb-sprof`.

**Read**

- [Cookbook: Scripting and executables](https://lispcookbook.github.io/cl-cookbook/scripting.html)
- [Google Common Lisp Style Guide](https://google.github.io/styleguide/lispguide.xml)
- [Lisp Project Ideas (inspiration)](https://github.com/azzamsa/awesome-lisp-companies)

## Day 30: Capstone, part 2: polish, ship, plan next steps

**Goal:** finish and package.

**Do today**

- Add the visualization/dashboard layer and a clean README with run instructions.
- Build a standalone executable: `(sb-ext:save-lisp-and-die "mytool" :toplevel #'main :executable t)` (try `:compression t` if your SBCL build supports it). Also try [ASDF's `program-op`](https://asdf.common-lisp.dev/asdf.html) or [Roswell](https://github.com/roswell/roswell) for scripts.
- Run your tests, profile once more, and write down three things Lisp did better than Python for you and three things Python still does better.

**Next steps (beyond the month)**

- Read *Practical Common Lisp* chapters 24-31 (binary parsing, a spam filter, an MP3 server, an HTML generator).
- Study *On Lisp* and *Let Over Lambda*-style advanced macros once you are comfortable.
- Work through [Common Lisp Recipes](https://weitz.de/cl-recipes/) (Edi Weitz) and [Lisp Koans](https://github.com/google/lisp-koans).
- Join the community: [r/Common\_Lisp](https://www.reddit.com/r/Common_Lisp/), [Lisp Discord/IRC listings on CLiki](https://www.cliki.net/), the [Common Lisp Foundation](https://common-lisp.net/), and the [Lisp Cookbook GitHub](https://github.com/LispCookbook/cl-cookbook) for contributions.
- Deepen the ML side: contribute examples to Lisp-Stat, cl-waffe2 or NUMCL, or write a wrapper for a library you need.

---

# Appendix: Cheat sheets and setup snippets

**Minimal Emacs `init.el` additions**

```elisp
(require 'package)
(add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
(package-initialize)
(load (expand-file-name "~/quicklisp/slime-helper.el"))
(setq inferior-lisp-program "sbcl")
;; install via M-x package-install: paredit, rainbow-delimiters
(add-hook 'lisp-mode-hook #'paredit-mode)
(add-hook 'lisp-mode-hook #'rainbow-delimiters-mode)
(add-hook 'slime-repl-mode-hook #'paredit-mode)
```

**Python to Lisp quick map**

| Python | Common Lisp |
| --- | --- |
| `def f(x, y=1): ...` | `(defun f (x &optional (y 1)) ...)` |
| `[x*x for x in xs]` | `(mapcar (lambda (x) (* x x)) xs)` |
| `d = {}; d[k] = v` | `(setf (gethash k d) v)` |
| `try / except` | `handler-case` |
| `with open(f) as s:` | `(with-open-file (s f) ...)` |
| `class A(B): ...` | `(defclass a (b) (...))` |
| `pip install x` | `(ql:quickload :x)` |
| `import numpy as np` | `(ql:quickload :numcl)` |
| `print(f"{x}")` | `(format t "~a~%" x)` |

**Daily rhythm that works**

1. 20 min: read the day's chapter or notes.
2. 60 to 90 min: type every example at the REPL; don't copy-paste.
3. 30 min: do the practice exercise from memory, then check.
4. 5 min: write a one-line note of what surprised you in a `journal.org` file in Emacs.
