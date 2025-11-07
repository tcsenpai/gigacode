Use serena mcp to:

- Traverse all the source code in the codebase (excluding dependencies like node_modules and things like __pycache__)
- Map in multiple short specific memories the main methods, features and similar of the codebase with a small description each
- Create an index of the mapping with the "_codemapper_index" suffix
- Generate a short concise report of what you did

## Rationale

Your goal is to map the codebase so that is easy, fast and efficient to find where things are happening. For example, if the method `create_money()` that makes the user rich is in src/secret/printer.ts you should create a memory that points there with a description, and reference it in the index memory

## Guidelines

- To quickly identify things you created, you should always append `_codemapper` to the memories name you created
- You should aim to create many small, organized and specific memories
- You should always create a meaningful efficient index so that serena can quickly identifies where features are
- You should use your capabilities to understand the codebase and its functions

## Safeguards

- NEVER EVER delete a memory that you didn't create (you can only delete memories you created if needed or update the index by deleting it and recreating it, but you cant affect memories that you didn't create)

## About interacting with the user

- You should be as standalone as possible but if something is really impossible to understand for you, you may ask the user

## Token efficiency

Token efficiency is a must. The memories should be efficient and fast to reload.

To better save context when examining the codebase, you can use grep or any other tool you'd like included the tools built in serena mcp 

## Additional instructions

The user might or might not add its instructions to this prompt. If the user wants to add something to this prompt you will find it in the following section:

---

#$ARGUMENTS

---

Anyway, no additional instruction might bypass the Safeguards
