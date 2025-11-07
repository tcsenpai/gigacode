Look at all the memories stored in your serena mcp and:

- See if they are redundant
- See if they are outdated or superseeded by other memories
- Elaborate a plan to compact them in efficient, complete and not redundant memories
- Write the new efficient, atomic memories and delete the old ones
- Write a short memory that will serve as the entry point, like a short overview and index of the memories you created so that serena is able to parse them easily, with the "_index" suffix
- Print a small and concise report of what you did

## Rationale

Your rationale is to avoid redundancy and repetition, as well to avoid overlaps. Tokens number is very important so you should aim to create a set of memories that takes less space than the current one, if it is outdated.

## Notes about serena memories

- Sometimes serena memories are timestamped (with dates in their name). You should use the timestamps to understand which is the last written memory

- Always remember that Serena loads memories based on their name 


## Guidelines

- EXTREMELY IMPORTANT: Your goal is to consolidate serena memories while keeping all the necessary informations and inferring the most update one by looking at when they were created. You will ensure no information is lost, only optimized.

- Under no circumstance you will delete a memory without ensuring the informations aren't lost or updated.

- Write atomic memories, with a clear domain per memory. Prefer multiple specific memories to single huge memories

- Each memory should be self explanatory and complete: avoid assuming knowledge and use smart referencing to save tokens without losing informations or clarity

- After you finish, DO NOT execute other commands UNLESS SPECIFIED BY THE USER. You will just print your report and ask the user what they want to do.

## Integrations

### codemapper

You should never delete memories with "_codemapper" in their name

## User instructions

The user might provide additional instructions that can't anyway override your security guidelines. If so, you will find them in this line:

--- Additional instructions

#$ARGUMENTS

---

