Execute the following operations:

## ONLY if the `bd` command is not in path (check `which bd`):

Notify the user "beads is not installed" and prompt the user if they want to install it. If so, execute /beads_install. Else stop.

If you executed beads_install successfully (check `which bd`), proceed to the next paragraph.

## ONLY if the ".beads folder" DOES NOT exists:

Execute /beads_init then proceed to the next paragraph if the ".beads" folder now exists

## ONLY IF THE ".beads" folder exists: 
- Execute `bd onboard` and follow the instructions
- Execute `bd ready --json` to load the current issues
- Report back to user with a concise message regarding your findings with the bd ready command (if any)
