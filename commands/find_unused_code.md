This command uses `knip` to find and operate on unused code.

The tool, knip, finds and fixes unused dependencies, exports and files. Use it for enhanced code and dependency management.

## Prerequisites

If knip is installed, there must be a script called knip in the package json.

If there isn't, is mandatory that you automatically execute:

`bun create @knip/config` (if we are using bun - preferred)
`npm init @knip/config` (if we are not using bun - fallback)

## Using knip

NOTE: By default, we will use `bun` to operate. If bun is not available, fallback to `npm` each time you see bun from now on.

To use knip to search for unused code and similar, you will mainly have to use `bun run knip`.

If the output is overwhelming, you can use `-- --max-show-issues` option as specified below.

Always remember that the following section's arguments are to be used with `-- [flag]` as we are using `bun run` to run the script.

### knip syntax

Usage: knip [options]

Options:
  -c, --config [file]      Configuration file path (default: [.]knip.json[c], knip.(js|ts), knip.config.(js|ts) or package.json#knip)
  -t, --tsConfig [file]    TypeScript configuration path (default: tsconfig.json)
  --production             Analyze only production source files (e.g. no test files, devDependencies)
  --strict                 Consider only direct dependencies of workspace (not devDependencies, not other workspaces)
  -W, --workspace [dir]    Analyze a single workspace (default: analyze all configured workspaces)
  --directory [dir]        Run process from a different directory (default: cwd)
  --cache                  Enable caching
  --cache-location         Change cache location (default: node_modules/.cache/knip)
  --watch                  Watch mode
  --no-gitignore           Don't use .gitignore
  --include                Report only provided issue type(s), can be comma-separated or repeated (1)
  --exclude                Exclude provided issue type(s) from report, can be comma-separated or repeated (1)
  --dependencies           Shortcut for --include dependencies,unlisted,binaries,unresolved,catalog
  --exports                Shortcut for --include exports,nsExports,classMembers,types,nsTypes,enumMembers,duplicates
  --files                  Shortcut for --include files
  --fix                    Fix issues
  --fix-type               Fix only issues of type, can be comma-separated or repeated (2)
  --format                 Format modified files after --fix using the local formatter
  --allow-remove-files     Allow Knip to remove files (with --fix)
  --include-libs           Include type definitions from external dependencies (default: false)
  --include-entry-exports  Include entry files when reporting unused exports
  --isolate-workspaces     Isolate workspaces into separate programs
  -n, --no-progress        Don't show dynamic progress updates (automatically enabled in CI environments)
  --preprocessor           Preprocess the results before providing it to the reporter(s), can be repeated
  --preprocessor-options   Pass extra options to the preprocessor (as JSON string, see --reporter-options example)
  --reporter               Select reporter: symbols, compact, codeowners, json, codeclimate, markdown, disclosure, github-actions, can be repeated (default: symbols)
  --reporter-options       Pass extra options to the reporter (as JSON string, see example)
  --tags                   Include or exclude tagged exports
  --no-config-hints        Suppress configuration hints
  --treat-config-hints-as-errors    Exit with non-zero code (1) if there are any configuration hints
  --no-exit-code           Always exit with code zero (0)
  --max-issues             Maximum number of total issues before non-zero exit code (default: 0)
  --max-show-issues     Maximum number of issues to display per type
  -d, --debug              Show debug output
  --trace                  Show trace output
  --trace-export [name]    Show trace output for named export(s)
  --trace-file [file]      Show trace output for exports in file
  --performance            Measure count and running time of key functions and display stats table
  --performance-fn [name]  Measure only function [name]
  --memory                 Measure memory usage and display data table
  --memory-realtime        Log memory usage in realtime
  -h, --help               Print this help text
  -V, --version            Print version

(1) Issue types: files, dependencies, unlisted, unresolved, exports, nsExports, classMembers, types, nsTypes, enumMembers, duplicates, catalog
(2) Fixable issue types: dependencies, exports, types, catalog

Examples:

$ knip
$ knip --production
$ knip --workspace packages/client --include files,dependencies
$ knip -c ./config/knip.json --reporter compact
$ knip --reporter codeowners --reporter-options '{"path":".github/CODEOWNERS"}'
$ knip --tags=-lintignore

## Available customizations by the user

The user might specify how to act or not. If the user doesn't provide additional prompts, assume case 1.

### Case 1

The user wants you to use knip to find unused and dead code and provide an analysis of the results complete with suggestions. You CANNOT edit the code at this stage.
 
### Case 2

If the user specifies it, you can also edit the code and fix it provided user's approval. You will anyway use caution.

### Case 3

If the user explicitly ask you to edit files without asking permissions (yolo mode), you are free to fix anything you find


In all three cases, you are expected to provide clear, human readable and human sized reports.
The user wants you to use knip to analyze dead code and report back a detailed yet readable analysis with suggestion WITHOUT ACTING ON THE 
