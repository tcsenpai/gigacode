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
  

## Install

- Clone the repository in a folder (assuming ~/gigacode)
  

### Claude Code

`ln -s ~/gigacode ~/.claude/commands/gigacode`

### Codex

`ln -s ~/gigacode ~/.codex/prompts/gigacode`

## Opencode

`ln -s ~/gigacode ~/.opencode/command/gigacode`

## Commands

TODO

### Branch Explainer

### Codemapper

### Optimize Serena Memories

### Review

### Simplifier
