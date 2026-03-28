# 42 Minishell

## Team
This project was developed by:
- Patvakanyan: https://github.com/Patvakanyan
- RubenBarkhudaryan: https://github.com/RubenBarkhudaryan
- @Patvakanyan

## Overview
This project is a small shell written in C that reproduces core Bash behavior under 42 constraints.

It handles command parsing, expansion, execution, redirections, pipes, and builtins with proper signal behavior.

At a high level, minishell is about transforming user input into a safe execution plan:
1. Read the command line.
2. Tokenize it while respecting quotes and special operators.
3. Build a structured representation (AST).
4. Expand variables and wildcards where appropriate.
5. Execute with correct process, fd, and signal handling.

## Features
- Interactive command loop using readline
- Tokenizer with quote-aware parsing
- AST-based execution pipeline
- Operators: pipe, logical AND/OR, subshell nodes
- Redirections: input, output, append, heredoc
- Variable expansion and wildcard support
- Builtins:
  - echo: printing with newline flag support
  - cd: directory changes with environment updates
  - pwd: current working directory output
  - export: environment insertion/update and sorted display
  - unset: environment variable removal
  - env: current exported environment display
  - exit: shell termination with status handling
- Correct exit status propagation and signal handling

## Core Concepts

### Parsing and AST
Instead of executing tokens directly, the shell builds an Abstract Syntax Tree. This makes precedence and grouping explicit for operators like pipes, logical AND/OR, and subshells.

Why this matters:
- `cmd1 && cmd2` should execute `cmd2` only when `cmd1` succeeds.
- `cmd1 || cmd2` should execute `cmd2` only when `cmd1` fails.
- Pipelines need connected file descriptors and process coordination.

### Process Model
Builtins may run in the parent process when needed (for state changes like `cd`), while external binaries are launched in child processes through fork/exec.

### Redirections and Heredoc
Redirection handling attaches command I/O to files or pipes and restores descriptors as needed. Heredoc uses a dedicated flow to collect multi-line input safely before execution.

### Signal Behavior
Interactive shell behavior differs between parent and child contexts. Handling `Ctrl-C` and `Ctrl-\` correctly is critical so the shell remains usable and reports statuses as expected.

## Build
- make

## Run
- ./minishell

Dependencies usually required on Linux:
- readline
- ncurses

## Example Commands
- echo hello
- export USERNAME=test
- env | grep USERNAME
- cat file.txt | grep word | wc -l
- ls *.c
- (echo one && echo two) || echo fail

Additional examples:
- cat infile | grep key > out.txt
- echo "$HOME"
- cat << EOF
  line 1
  line 2
  EOF

## Project Structure
- tokenizer: lexical analysis
- ast: syntax tree and parse utilities
- expander: env var and wildcard expansion
- redirection: fd and heredoc handling
- execute: builtin/external execution and pipes
- env: environment parsing and management
- signals: shell and child signal behavior
- built_in: builtin command implementations
- libft: internal utility library

Execution flow in practice:
- input -> tokens -> AST -> expansion -> redirections/pipes -> builtin or execve -> exit status

## Key Learnings
- Shell architecture from input to execution
- Process control and file descriptor management
- Parser and AST design
- Robust memory and signal handling in interactive programs

## Challenges Solved
- Keeping parser behavior consistent with quoted/unquoted text
- Preserving shell state for builtins while still supporting pipelines
- Avoiding leaks in many failure paths (parse errors, redirection failures, interrupted heredoc)
- Returning accurate POSIX-like exit statuses

## Notes
This implementation is educational and follows 42 project constraints and coding style requirements.
