Use the following command

`coderabbit review --plain`

to generate a plaintext PR review for all the changes committed in this branch, following the Workflow Guidelines defined below.

You will then read them and analyze them to spot hallucinations, false positive or similar invalid issues, then you will compile a new file following the Workflow Guidelines below with a polished review divided by CRITICAL, HIGH, MEDIUM, LOW, OPTIONAL issues with possible fixes.

## Coderabbit command help

The basic command to use is `coderabbit review --plain`. However, be aware that the help command for `coderabbit` says:

Usage: coderabbit review [options]

AI-driven code reviews with interactive or plain text output (default)

Options:
  -V, --version            output the version number
  --plain                  Output in plain text format (non-interactive)
  --prompt-only            Show only AI agent prompts (implies --plain)
  -t, --type <type>        Review type: all, committed, uncommitted (default: "all")
  -c, --config <files...>  Additional instructions for CodeRabbit AI (e.g., claude.md, coderabbit.yaml)
  --base <branch>          Base branch for comparison
  --base-commit <commit>   Base commit on current branch for comparison
  --cwd <path>             Working directory path
  --no-color               Disable colored output
  -h, --help               display help for command

You might want to use `-t` , `-.base` , `--base-commit` , `--cwd` if it is useful.

If you deem necessary, use `-c` with CLAUDE.md or whatever documentation is necessary.

## Important informations

- The command will take a lot of time, sometimes even 10 minutes. Use an appropriate timeout
- Unless explicitly specified by custom instructions, ignore all the issues referencing .md (markdown) files as they are not part of the codebase
- The review will provide prompts for agents that are very useful for you, as well as snippets. Harness all the important and useful information from the review

## Workflow Guidelines

You will generate the review using the above command redirecting it to a `PR_REVIEW_RAW.md` file.

You will generate the polished review in a `PR_REVIEW_FINAL.md` file.

You will then delete `PR_REVIEW_RAW.md`.

Your output will be a concise but complete explanation of the review together with actionable suggestions and references to the polished file.

## Custom instructions

The user might or might not add custom instructions to this command. If there are, they will be in the following line:

--- Additional instructions

#$ARGUMENTS

---


