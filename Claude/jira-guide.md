# Jira Guide

## Screenshots

- Every ticket about UI includes a screenshot. UI means anything that changes what the user sees: layout, copy, colors, icons, animation, or a new screen.
- A bug shows the broken state. A feature or change shows the current screen, or the design when the screen does not exist yet.
- Put the screenshot at the top of the description, embedded inline, above the text. The reader should see the screen before reading about it.
- Do not add it only as a standalone attachment. Embedding it already lists it under the ticket's attachments, so attaching it separately adds a second copy. That listed entry backs the inline image; do not delete it.
- If you have no screenshot, ask the user for one before creating the ticket.

## Description

- Start the description with the problem itself, in plain sentences, below the screenshot when there is one. Do not put a "Problem" or "Summary" heading above it.
- A bug written for developers shows the code that causes it, as code blocks. When the code makes the trigger easy to understand, leave out reproduction steps; otherwise explain how to reproduce it. Leave out an Impact section.
