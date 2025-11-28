# Gigacode

An (almost) ready-to-use set of commands for agentig AIs such as codex or Claude Code

## What is Gigacode

Gigacode is a set of commands I created for myself aiming at make my workflow as a developer using agentic tools as smooth, useful and integrated as possible.

Many of the following commands requires a basic environment that will be discussed below and, if needed, in the corresponding command paragraph.

## Basic Environment

### Required

- You are expected to use either [Claude Code](https://code.claude.com/docs/en/setup), [Codex CLI](https://developers.openai.com/codex/cli/) or [Opencode](https://opencode.ai/docs/) (note that when using Opencode you may use any LLM provider you want, the commands should be agnostic about them)

- Most of the commands assume you are using the [Serena MCP server](https://github.com/oraios/serena). In the linked repo you will find instructions to install it in [Codex](https://github.com/oraios/serena?tab=readme-ov-file#codex) , [Claude Code](https://github.com/oraios/serena?tab=readme-ov-file#claude-code) and others (such as Gemini-CLI / Qwen or Opencode.

### Optional

Some commands will require additional dependencies which will be discussed in the relevant paragraphs. However, if you want to blindly install all the dependencies needed for all the commands, please install:

- [CodeRabbit CLI | AI Code Reviews in CLI](https://www.coderabbit.ai/cli)

As well as the above mentioned Serena MCP Server.

## Install

- Clone the repository in a folder (assuming ~/gigacode)

### Claude Code

`ln -s ~/gigacode/commands ~/.claude/commands/gigacode`

### Codex

`ln -s ~/gigacode/commands ~/.codex/prompts/gigacode`

## Opencode

`ln -s ~/gigacode/commands ~/.opencode/command/gigacode`

## Commands

This is a complete list of commands enabled in Gigacode.

For each command, you might find a "Supporting Customization" badge.

If present, it means that you can add a custom prompt to the command to influence its behavior (e.g. `/command but talk in pirate language`).

### Branch Explainer

The `branch_explainer` command works by detecting if we are in a branch (non-default branch) and, if we are, it:

- Analyze diffs compared to the default branch

- Understand the scope of the current branch

- Work together with the `codemapper` command (see below) to leverage knowledge if present

- Produce a clear and concise output to explain what the branch is about

### Codemapper

_✅ Supporting Customization_

The `codemapper` command leverages Serena efficient search and analyze capabilities to traverse the whole codebase and extract relevant informations about the scope and the location of methods, types, symbols and other parts of the code.

It creates `_codemapper` suffixed memories in .serena/memories that contains its findings in a token efficient and indexed way, divided by scope or areas.

It enables a quick retrieval by any other tool and your agent itself of the main features of the code, saving tokens, time and errors in the medium-long run.

Running this command periodically is advised, especially if the code changes significantly.

### Optimize Serena Memories

_✅ Supporting Customization_

### Review

_✅ Supporting Customization_

### Simplifier

_✅ Supporting Customization_

### Beads Related Commands

The following commands are used to interact and setup or reload Beads ( see [Beads - A memory upgrade for your coding agent](https://github.com/steveyegge/beads) )

While beads itself is largely autonomous, the Gigacode provided commands help in setting up, loading and managing beads in a complex workflow.

#### `beads_load`

This is the "catch-all" command that you will most likely want to use.

It detects id beads and its mcp are installed and, if they are not, asks the user if to install them. If the user confirms, it installs them and, in case of Claude Code being detected, automatically install the beads-mcp into Claude Code. For this operation it calls `beads_install`.

After this step, it detects if beads is configured for the project. If not, it calls `beads_init` to create the initial configuration using `bd` native options.

When beads is already configured (or after the previous step), it calls `bd ready --json` to let your agent load beads memories.

#### `beads_install`

While available for direct usage, this command is mainly used for `beads_load`.

It provides an automatic way to install beads from the official repository, install `beads-mcp` through either uv (preferred) or pip and install it in Claude Code if available.

#### `beads_init`

This command is, once again, mainly used by `beads_load`. However, it can be used autonomously to set up beads for a specific repository.

The command initialize beads in your repository, installs git hooks for beads and run the `bd_onboard` command to load memories and ensure agentic awareness.
