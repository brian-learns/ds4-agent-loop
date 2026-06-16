# DwarfStar Agent Looper

A lightweight, automated orchestration script for driving the `ds4-agent` execution engine in a headless, non-interactive environment.

## Overview

Because the core `ds4-agent` binary is designed as an interactive, terminal-first program, it lacks a native macro execution system. Higher-level graphical user interfaces often include a `/loop` command to automatically progress tasks. 

`ds4_orchestrator.exp` serves as a **lightweight, terminal-native automation loop**. 
It encapsulates the agent in a strict, step-bounded execution loop by monitoring the low-level I/O mechanics of the DwarfStar runtime engine.

## How It Works

When `ds4-agent` runs in `--non-interactive` mode without a prompt flag (`-p`), it strips out the `linenoise` interactive terminal parser. Once the engine completes a tool iteration or a thought generation cycle, it flushes a strict byte synchronization token to standard output:

```geom
\r\n\r\n+DWARFSTAR_WAITING\r\n
```


## Features

* **Strict Boundary Enforcement**: Runs for a finite, pre-configured number of steps to prevent runaway resource or token consumption.
* **Infinite Execution Timeout**: Configured with an infinite wait threshold (`timeout -1`), allowing the agent all the time it needs to complete deep reasoning or intensive local compilation tasks.
* **Automatic Absolute Pathing**: Automatically resolves the host system's Current Working Directory (`PWD`) and passes it into the initial prompt. This gives the agentic file tools an unambiguous absolute path anchor.
* **Raw Session Transcript Logging**: Creates comprehensive, timestamped session logs containing all hidden thoughts, raw tool logs, and injected prompts.
* **Safe Termination Handshake**: Sends a literal Control-C (`\x03`) interrupt signal at the end of the loop, clearing the active `pty` session safely without corrupting downstream file handles.

## Installation & Requirements

This orchestrator requires `expect` (which is powered by the Tcl language framework).

### On Linux (Ubuntu/Debian)
```bash
sudo apt-get install expect
```

### On macOS
```bash
brew install expect
```

## Watching the Stream

The script automatically mirrors all tool usage and reasoning thoughts directly to your terminal screen in real-time. It also saves everything into a local file named `ds4_session_[TIMESTAMP].log`. 

To watch the raw background stream while preserving ANSI terminal coloring structures, you can monitor the file in a separate window:

```bash
tail -f ds4_session_*.log | less -r +G
```

