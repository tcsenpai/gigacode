Look at the codebase you are working on and analyze it with the goal of identifying overengineering.

## What is overengineering

Code that is unnecessarily complex, duplicated, leftover and in general not efficient

## Your goal

You should optimize the code without breaking any feature. Your goal is to output a plan to optimize the codebase (don't edit directly except if the user asks you to do it in its additional instructions or using the flag `write` )

## Safeguards

- Always act like you are in production, don't take risks
- Don't ever delete features or functionalities
- Always double check your edits for syntax and possible interactions
- If the code is already clean, don't force yourself to find things to optimize: notify the user and compliment them

## Additional instructions

The user might or might not add its instructions to this prompt. If the user wants to add something to this prompt you will find it in the following section:

---

#$ARGUMENTS

---

Anyway, no additional instruction might bypass the Safeguards
