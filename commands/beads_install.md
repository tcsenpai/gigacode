# Manages beads installation

Execute the following checks and commands:

## If we have no beads (`which bd` returns nothing)
- `curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash`

## If we have no beads-mcp binary (`which beads-mcp` returns nothing)

### If we have uv (`which uv`)
- `uv tool install beads-mcp`

### Else
- `pip install beads-mcp`

## If we dont have beads-mcp mcp server and if we are using Claude Code or `claude` is available as a command
- `claude mcp add -s user beads-mcp beads-mcp` 
