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

The `optimize_serena_memories` command is a pretty useful tool that is aimed to Serena users and is useful to condensate and tidy up memories that builds up in Serena.

By default, Serena is pretty conservative with memories and this is a good thing. Anyway, after some intense usage or after some time, memories in Serena (located in .serena/memories) tend to build up and become a bit redundant or outdated.

This command aims to condensate, clean up and clear up Serena memories without deleting important informations and being token efficient. Memories created by the `codemapper` command are preserved.

You can specify more instructions through custom prompts after the command.

**Warning:** while being usually safe, this command has the potential to mess up with your codebase memories. Please either back them up or specify what to keep using custom prompting. Memories considered outdated, reduntant or superseeded will be merged or deleted.

### Review

_✅ Supporting Customization_

_⚠️Requires CodeRabbit (see above)_

The `review` command conduct an in-depth review of the committed code compared to the default branch (or, if specified by the customization, any other branch) using `coderabbit` CLI tool.

It makes your agent fully aware of the coderabbit CLI tool syntax and functionalities and, by default, examine **committed** files.

It generates a "PR_REVIEW_RAW.md" file in the claudedocs folder, then it will analyze it, detect hallucinations or false positives, compare it to the actual code reviewed and generate a "PR_REVIEW_FINAL.md" file in the claudedocs folder, deleting the raw one.

It will then output to the user a summary of the findings including suggestions, possible autofixes and context-aware comments.

This is a quite powerful tool, and should handle errors in a pretty nice manner.

It can take anywhere from 1 to 10 minutes depending on coderabbit status, plan and number of files to examine.

You can also specify additional instructions using a custom prompt after the command.

By default, CLAUDE.md files are included as additional instructions if present.

### Simplifier

_✅ Supporting Customization_

***Warning: this command can be highly destructive and should be treated as a dangerous, experimental command***

The `simplifier` command cleans up dead code, tries to remove over engineering and look for leftovers, duplicate logic and unused code.

By default it tries to optimize the entire codebase paying attention not to break features.

It contains many safeguards aimed at being as conservative as possible but always commit before using it so that you can reset changes in case something happens.

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

### Find Unused Code

_✅ Supporting Customization_

The `find_unused_code` command leverages [knip](https://knip.dev/) to detect unused code, dependencies, exports, and files in your project.

By default, it uses `bun` to run knip (falling back to `npm` if bun is unavailable). If knip is not configured in your project, the command will automatically set it up using `bun create @knip/config` or `npm init @knip/config`.

The command operates in three modes based on user input:

1. **Analysis Mode (Default)**: Runs knip and provides a detailed, human-readable analysis of findings with suggestions. No code modifications are made.

2. **Approval Mode**: When specified, the agent can edit code to fix issues but will ask for user approval before each change.

3. **YOLO Mode**: When explicitly requested, the agent will automatically fix all issues it finds without asking for permission.

This is particularly useful for maintaining clean codebases by identifying:
- Unused files
- Unused dependencies and devDependencies
- Unused exports and types
- Duplicate exports
- Unlisted or unresolved dependencies
