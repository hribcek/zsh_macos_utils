# **ZSH Shell Scripting Coding Style**

When generating (or refactoring) zsh scripts in this workspace, strictly adhere to the following rules:

## **1. Environment & Dependencies**

* **Target OS:** Assume macOS Sequoia 15.7.x.

* **Tooling:** Assume GNU coreutils and GNU findutils and some other GNU commands are installed and prioritized in the PATH. This is the list of commands which invoke GNU versions instead of BSD versions: `awk`, `b2sum`, `base32`, `base64`, `basename`, `basenc`, `cat`, `chgrp`, `chmod`, `chown`, `chroot`, `cksum`, `comm`, `cp`, `csplit`, `cut`, `date`, `dd`, `df`, `dir`, `dircolors`, `dirname`, `du`, `echo`, `egrep`, `env`, `expand`, `expr`, `factor`, `false`, `fgrep`, `find`, `fmt`, `fold`, `grep`, `groups`, `head`, `hostid`, `id`, `install`, `join`, `link`, `ln`, `locate`, `logname`, `ls`, `lsof`, `md5sum`, `mkdir`, `mkfifo`, `mknod`, `mktemp`, `mv`, `nice`, `nl`, `nohup`, `nproc`, `numfmt`, `od`, `paste`, `pathchk`, `pinky`, `pr`, `printenv`, `printf`, `ptx`, `pwd`, `readlink`, `realpath`, `rm`, `rmdir`, `sed`, `seq`, `sha1sum`, `sha224sum`, `sha256sum`, `sha384sum`, `sha512sum`, `shred`, `shuf`, `sleep`, `sort`, `split`, `stat`, `stdbuf`, `stty`, `sum`, `sync`, `tac`, `tail`, `tar`, `tee`, `test`, `timeout`, `touch`, `tr`, `true`, `truncate`, `tsort`, `tty`, `uname`, `unexpand`, `uniq`, `unlink`, `updatedb`, `users`, `vdir`, `wc`, `who`, `whoami`, `xargs`, `yes`.

* **Flags:** Strictly use GNU flags and features without BSD compatibility workarounds. If a BSD-specific command is unavoidable because no GNU equivalent exists, you must document the exception in the code and invoke the BSD binary via its absolute path (e.g., `/bin/ls -dlO <path>`).

* **Dependencies:** External dependencies must be mentioned at the very top of the file as a comment. Assume coreutils and popular modules are already installed.

* **Shebang:** Use `#!/usr/bin/env zsh` unless the script is running as a LaunchAgent, which requires `#!/bin/zsh`.

## **2. Formatting, Indentation & Whitespace**

* **Indentation:** Use exactly 2 spaces, NO TABS. Code blocks must be indented by 2 spaces relative to the enclosing statement.

* **Multi-line Conditions:** Indent continuation lines by an additional 2 spaces (total 4\) relative to the start of the statement to avoid visually colliding with the body indentation in conditions. For example:
  ```zsh
  if (( count < 10 )) && \
     [[ -f "${file}" ]]; then
    # body
  fi
  ```

* **Trailing Whitespace:** Output must contain zero trailing whitespace on any line.

* **Empty Lines:** Empty lines must be completely empty (0 characters) and must not be indented.

* **Line Limits:** Each command must be on its own line. Combining commands with `;` or `&&` on a single line is prohibited (except for loop headers and conditional statements).

## **3. Variables & Naming**

* **Naming Convention:** User-defined variables must use `camelCase`. In rare situations, `snake_case` is allowed as well, but you must never mix the two within a single script. Do not use `UPPER_CASE` for user-defined variables within the script. Exceptions are existing system/environment variables (e.g., `${PATH}`, `${HOME}`).

* **Referencing:** In standard contexts, you must always wrap variables in curly braces. This strictly applies also to special and positional parameters as well (e.g., `${?}`, `${$}`, `${1}`).

## **4. Conditionals & Math**

* **String/File Tests:** Strictly use double brackets with space `[[ ... ]]`. Never use single brackets `[ ... ]` or test.

* **Arithmetic Tests:** Use double parentheses with space `(( ... ))`. This is preferred test over `[[ ... ]]` for numeric comparisons. Do not use `let` or `expr` for arithmetic tests. Even if you have arithmetic test combined with string/file tests, you must still use `[[ ... ]]` for string/file tests and `(( ... ))` for arithmetic tests, and combine them with `&&` or `||` as needed. For example:\
  Do this:
  ```zsh
    if [[ -f "${file}" ]] && (( count < 10 )); then
      # body
    fi
  ```
  instead of this:
  ```zsh
    if [[ -f "${file}" && "${count}" -lt 10 ]]; then
      # body
    fi
  ```

* **Variables in Math:** Inside `(( ... ))`, omit the `${}` syntax for variables and use bare names of variables. Exception is for special expansions like array lengths (e.g., `(( ${#array} > 0 ))`), which require curly braces.

## **5. Control Flow & Keyword Spacing**

* **Explicit Blocks:** Use explicit `if ... then ... fi` or `if ... then ... else ... fi` or `if ... then ... elif ... then ... else ... fi` blocks. Do not use short-circuit logic (e.g., `[[ ... ]] && cmd1 || cmd2` or `[[ ... ]] && cmd1` or `[[ ... ]] || cmd2`) as a standalone statement replacement for an if block.

* **Keyword Placement:** Keywords like `then` and `do` must be on the same line as the condition. And this is the only time you can have multiple commands on the same line - e.g., `for var in ...; do` or `while ...; do` or `if ...; then`.

* **Semicolon Spacing (CRITICAL):**
  * Only if the condition ends with a double closing bracket `]]` or double parenthesis `))`, you must OMIT SPACE before the semicolon (e.g., `if [[ "${var}" == "foo" ]]; then ...` or `while (( count < 10 )); do ...`).

  * If the condition doesn't end with a double closing bracket `]]` or double parenthesis `))`, you must KEEP ONE SPACE before the semicolon (e.g., `if grep -q "foo" "${file}" ; then ...` or `while read -r line ; do ...`).
