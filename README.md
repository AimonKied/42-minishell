# 🐚 Minishell

<p align="center">
  <img src="https://img.shields.io/badge/Language-C-blue.svg" alt="Language"/>
  <img src="https://img.shields.io/badge/Grade-125%2F100-brightgreen.svg" alt="Grade"/>
  <img src="https://img.shields.io/badge/42-Heilbronn-orange.svg" alt="School"/>
</p>

> **As beautiful as a shell** — A fully functional UNIX command-line interpreter built from scratch in C

## 📋 Overview

**Minishell** is a project from the 42 School curriculum that challenges students to recreate a simplified version of Bash. This project demonstrates deep understanding of process management, file descriptors, signal handling, and parsing techniques — all implemented in pure C following strict coding standards.

This shell implementation handles real-world command-line scenarios including piping, redirections, environment variables, and built-in commands, providing a fully interactive shell experience.

## ✨ Features

### 🎯 Core Shell Functionality
- **Interactive & Non-Interactive Modes** — Full support for both terminal and scripted usage
- **Command History** — Navigate through previous commands using arrow keys (via GNU Readline)
- **Signal Handling** — Proper handling of `Ctrl+C`, `Ctrl+D`, and `Ctrl+\` signals

### 🔧 Built-in Commands
| Command | Description |
|---------|-------------|
| `echo` | Display text with `-n` flag support |
| `cd` | Change directory with relative/absolute paths |
| `pwd` | Print current working directory |
| `export` | Set environment variables |
| `unset` | Remove environment variables |
| `env` | Display all environment variables |
| `exit` | Exit the shell with optional exit code |

### 🔀 Redirections & Pipes
- **Input Redirection** (`<`) — Read input from a file
- **Output Redirection** (`>`) — Write output to a file
- **Append Mode** (`>>`) — Append output to a file
- **Here Document** (`<<`) — Read input until a delimiter is reached
- **Pipes** (`|`) — Chain multiple commands together

### 🔤 Advanced Features
- **Environment Variable Expansion** — `$VAR` and `$?` (exit status)
- **Quote Handling** — Single (`'`) and double (`"`) quotes with proper escaping
- **Tilde Expansion** — `~` expands to home directory
- **Path Resolution** — Commands searched in `$PATH`

## 🏗️ Architecture

The project follows a modular architecture with clear separation of concerns:

```
┌─────────────────────────────────────────────────────────────┐
│                         MINISHELL                            │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌────────┐ │
│  │  LEXER   │ -> │  PARSER  │ -> │ EXECUTOR │ -> │ OUTPUT │ │
│  └──────────┘    └──────────┘    └──────────┘    └────────┘ │
│       │              │               │                       │
│       ▼              ▼               ▼                       │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐               │
│  │  Tokens  │    │  AST/    │    │ Process  │               │
│  │  Stream  │    │  Cmds    │    │ Mgmt     │               │
│  └──────────┘    └──────────┘    └──────────┘               │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 📁 Project Structure

```
minishell/
├── include/              # Header files
│   ├── minishell.h       # Main data structures
│   ├── lexer.h           # Tokenization definitions
│   ├── parser.h          # Parsing functions
│   ├── execute.h         # Execution & builtins
│   ├── garbage_collector.h
│   └── libft/            # Custom C library
│
├── src/                  # Source files
│   ├── main.c            # Entry point
│   ├── shell.c           # Shell loop & initialization
│   │
│   ├── lexer*.c          # Tokenization (7 files)
│   ├── parser*.c         # Command parsing (4 files)
│   ├── execute*.c        # Command execution (2 files)
│   ├── pipes*.c          # Pipeline handling (2 files)
│   ├── redirect.c        # I/O redirections
│   ├── heredoc*.c        # Here document handling (7 files)
│   │
│   ├── builtin.c         # Built-in dispatcher
│   ├── echo.c            # echo implementation
│   ├── cd.c              # cd implementation
│   ├── pwd.c             # pwd implementation
│   ├── env.c             # env implementation
│   ├── export*.c         # export implementation (3 files)
│   ├── unset.c           # unset implementation
│   ├── exit.c            # exit implementation
│   │
│   ├── env_*.c           # Environment management
│   ├── garbage*.c        # Memory management
│   └── signals.c         # Signal handling
│
└── Makefile              # Build configuration
```

## 🔧 How It Works

### 1️⃣ Lexical Analysis (Tokenization)
The lexer breaks raw input into meaningful tokens:
```
Input:  echo "Hello World" | grep Hello > output.txt
Tokens: [echo] ["Hello World"] [|] [grep] [Hello] [>] [output.txt]
```

### 2️⃣ Parsing
The parser constructs a command list with proper structure:
- Commands are organized into nodes
- Redirections are attached to their respective commands
- Pipes create linked command chains

### 3️⃣ Execution
The executor processes the command structure:
- **Single builtin**: Executed in the main process (preserves environment changes)
- **External commands**: Forked into child processes
- **Pipelines**: Multiple processes connected via pipe file descriptors

### 4️⃣ Memory Management
A custom garbage collector tracks all allocations, ensuring clean memory handling throughout the shell's lifecycle.

## 🚀 Getting Started

### Prerequisites
- **GCC** or **Clang** compiler
- **GNU Readline** library
- **Make** build system
- Linux/macOS operating system

### Installation

```bash
# Clone the repository
git clone https://github.com/AimonKied/42-minishell.git

# Navigate to project directory
cd 42-minishell

# Compile the project
make

# Run minishell
./minishell
```

### Compilation Flags
The project compiles with strict flags to ensure code quality:
```
-Wall -Wextra -Werror
```

## 💻 Usage Examples

```bash
# Start the shell
./minishell

# Basic command execution
msh-1.0$ ls -la

# Pipeline example
msh-1.0$ cat file.txt | grep "pattern" | wc -l

# Redirections
msh-1.0$ echo "Hello" > output.txt
msh-1.0$ cat < input.txt >> output.txt

# Here document
msh-1.0$ cat << EOF
> Line 1
> Line 2
> EOF

# Environment variables
msh-1.0$ export MY_VAR="Hello World"
msh-1.0$ echo $MY_VAR

# Exit with status code
msh-1.0$ exit 42
```

## 🛠️ Technical Highlights

### System Calls Used
| System Call | Purpose |
|-------------|---------|
| `fork()` | Create child processes |
| `execve()` | Execute external programs |
| `pipe()` | Create inter-process communication |
| `dup2()` | Redirect file descriptors |
| `wait()`/`waitpid()` | Synchronize parent/child processes |
| `signal()` | Handle system signals |
| `tcsetpgrp()` | Terminal process group management |

### Key Data Structures
- **Token List** — Linked list of lexical tokens
- **Command List** — Linked list of command nodes with associated files
- **Environment List** — Linked list managing environment variables
- **Heredoc List** — Linked list for here document content

## 📊 Skills Demonstrated

| Skill | Application |
|-------|-------------|
| **Process Management** | Fork, exec, wait, pipes |
| **Memory Management** | Custom garbage collector, leak-free code |
| **Signal Handling** | SIGINT, SIGQUIT, proper terminal control |
| **File I/O** | Redirections, here documents |
| **Parsing** | Lexical analysis, tokenization, syntax validation |
| **Data Structures** | Linked lists, dynamic arrays |
| **System Programming** | POSIX compliance, Unix internals |

## 👥 Team

This project was developed as a collaborative effort at **42 Heilbronn**:

- **swied** — Shell core, execution, builtins, heredocs
- **vramacha** — Lexer, parser, expansion, signals

## 📜 License

This project is part of the 42 School curriculum. Feel free to explore and learn from the code!

---

<p align="center">
  <i>Built with ❤️ at 42 Heilbronn</i>
</p>
