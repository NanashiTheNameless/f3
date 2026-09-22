---
name: commit-message
description: Rules and guidelines for generating Git commit messages in F3. Use when writing, formatting, or reviewing Git commit messages for this repository.
---

All commit messages in F3 must follow the project's established conventions.

```text
<subsystem>: <short summary in imperative mood>

<detailed description of changes>

<optional issue references>
```

1. **Subsystem Prefix (`<subsystem>`)**:
   - Matches the affected tool, library, directory, or configuration in lowercase.
   - **Applications / Binaries**:
     Use the binary name (e.g. `f3write`, `f3read`, `f3probe`, `f3brew`, `f3fix`).
     When a change touches multiple related tools, join them with a slash (e.g. `f3write/f3read`, `f3write/f3read/f3brew`, `f3brew/f3probe`).
   - **Libraries**:
     Use the library name (e.g. `libflow`, `libprobe`, `libutils`, `libdevs`, `libfile`).
   - **Build and CI**:
     Use `Makefile`, `GitHub Actions` (or `GitHub`).
   - **Documentation and Metadata**:
     Use `README`, `doc`, `man` (or specific manual page, e.g. `f3read.1`), `changelog`, `.gitignore`.
   - **Developer Tools and Workspace Configuration**:
     Use `agents`, `scripts`, `zed`.
   - **Project-Wide Changes**:
     Repository-wide restructurings or standard bumps may occasionally omit the subsystem prefix (e.g. `Move codebase from C99 to C17`, `Bump version to 10.0`, `Reorganize codebase with new directories`), but scoped changes should always use the subsystem prefix.

2. **Subject Line**:
   - **Format**: `<subsystem>: <imperative summary>` (separated by a colon and a single space).
   - **Casing**: Start the summary with a lowercase letter (e.g. `libflow: drop a magic number`, `f3write: track per-file min/max speeds`), unless the first word is a case-sensitive code identifier, macro, or proper noun (e.g. `README: improve instructions for FreeBSD`, `libutils: add generic macro MIN()`).
   - **Mood**: Use the imperative mood (e.g. `add`, `fix`, `drop`, `make`, `avoid`, `replace`, `adopt`, `employ`, `update`, `simplify`, `standardize`, `rename`, `convert`, `tighten`, `generalize`, `remove`, not `added`, `fixes`, `updating`).
   - **Punctuation**: Do not end the subject line with a period.
   - **Length**: Keep the subject line concise (under 72 characters, ideally 50–60 characters).
   - **Function and Symbol References**: When mentioning functions or commands in the subject, adhere to the reference rules below (e.g. `f3brew: drop assert() in validate_block()`, `libprobe: make find_first_bad_block() report more information`).

3. **Function, Command, and Symbol References**:
   - **Internal / Project Functions**:
     Always append empty parentheses `()` to function names and function-like macros:
     - Examples: `init_flow()`, `validate_block()`, `calc_avg_speed()`, `find_first_bad_block()`, `end_measurement()`, `assert()`, `MIN()`, `DIM()`.
   - **Standard Library Functions, System Calls, and System Commands**:
     Always append the manual section number in parentheses after the name of standard library functions, POSIX functions, system calls, and system administration commands:
     - **Section 1 (User Commands)**: `ls(1)`, `grep(1)`
     - **Section 2 (System Calls)**: `clock_gettime(2)`, `fdatasync(2)`, `read(2)`, `write(2)`, `getrandom(2)`
     - **Section 3 (C / Library Functions)**: `free(3)`, `aligned_alloc(3)`, `snprintf(3)`, `perror(3)`
     - **Section 8 (System Administration Commands)**: `losetup(8)`, `mount(8)`
   - **Types, Structs, and Macros**:
     Mention types with their C specifiers (e.g. `struct flow`, `struct perf_device`, `struct block_stats`, `uint64_t`), enum values in lowercase with prefix (e.g. `bs_changed`, `bs_good`, `bs_overwritten`), and macros/constants in uppercase (e.g. `SECTOR_ORDER`, `MEGABYTE_ORDER`, `UNUSED()`, `DIM()`, `FW_STEADY`).
   - **Parameters and Flags**:
     Mention CLI flags as written (e.g. `--max-write-rate`, `--verbose`, `--destructive`, `--fix-cmd`). Mention function parameters by name (e.g. `parameter measurement_boundary`, `parameter processed_blocks`).
   - **Plain Text Style**:
     Do not use Markdown backticks in commit messages. Write code identifiers, function names, file names, compiler options (e.g. `-O2`), and directives (e.g. `#include <unistd.h>`) as plain text. Single quotes (`'...'`) or double quotes (`"..."`) may be used for quoting exact phrases or compiler messages when clarity is needed.

4. **Message Body**:
   - **Separation**: Separate the subject from the body with a blank line.
   - **Line Wrapping**: Hard-wrap all body lines at 72 characters.
   - **Structure**:
     - State the problem, limitation, or background context first (e.g. what fails, what distortion happens, or why the current behavior is inadequate).
     - State the solution and technical rationale (what changed and why).
   - **Lists**: When breaking down multiple steps or changes, use numbered lists (`1. ...`, `2. ...`) or bullet points (`- ...`).
   - **Diagnostics**: Compiler warnings, error messages, and log snippets may be included verbatim in the body to document the issue clearly.

5. **Issue References**:
   - **Closing Issues**: When closing an issue, use the project's standard formula at the end of the body (separated by a blank line):
     ```text
     This commit closes #<issue-number>
     ```
     or:
     ```text
     This patch closes #<issue-number>
     ```
   - **Closing Multiple Issues**:
     ```text
     This commit closes #<n1>, closes #<n2>
     ```
   - **Non-Closing References**: When referencing an issue without closing it:
     ```text
     See issue #<issue-number> for an example.
     ```
     or:
     ```text
     This patch addresses issue #<issue-number>.
     ```

### Example 1: Function name with `()` and parameter rationale
```text
libflow: add parameter measurement_boundary to end_measurement()

Introduce parameter measurement_boundary to end_measurement(),
pass true in f3write.c and f3read.c since they make measurements
on files (i.e., a file is a measurement boundary), and pass
false for everyone else.

Reaching a boundary forces any leftover processed_blocks and
acc_delay_ns to be committed to the global statistics.
This prevents measurement data from bleeding across boundaries.
```

### Example 2: System call with section number and issue resolution
```text
f3write: gracefully handle failures of fdatasync(2)

According to issue #102, calls to fdatasync(2) might take
a long time (e.g. 3s or 4s) and distort the measurement of
the average write speed. This patch addresses it by only updating
the measurements when delays are within a tolerance.

This patch closes #102
```

### Example 3: CLI flag with motivation and issue reference
```text
f3brew: add flag --fix-cmd

When flag --fix-cmd is passed, f3brew shows how to call f3fix on
the largest good region identified.
```

### Example 4: Compiler warning / diagnostics verbatim in body
```text
f3probe: avoid compiler warning

When using -O2, GCC was issuing the following warning:

f3probe.c: In function 'main':
f3probe.c:446:13: warning: 'sdev' may be used uninitialized in this function [-Wmaybe-uninitialized]
   sdev_flush(sdev);
             ^

GCC could not deduce that args->save being true implied
sdev was not NULL.

This patch addresses issue #34.
```

### Example 5: Multi-tool change with numbered list
```text
f3write/f3read/f3brew: standardize report of I/O speeds

This commit makes f3write, f3read, and f3brew report I/O speeds
analogously to f3probe:
1. Emphasizing that these are sequential measurements.
2. Including the number of blocks and time measured.
3. Using "write" and "read" instead of "writing" and "reading".
```
