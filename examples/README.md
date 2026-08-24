# Examples

Starting points, not finished configurations. Both need editing before use.

## `settings.json`

Goes in `.claude/settings.json` at the top of your project. Replace
`/home/me/thesis` with your own project folder, and replace the websites
with the ones you actually look things up on.

The three lists do different jobs, and the order they appear in here is the
order the guide sets them up:

- **deny** — refused outright. The same list suits everybody.
- **ask** — Claude stops and checks with you. Commands that are fine in
  themselves but should not happen while you are not thinking.
- **allow** — goes ahead without asking. Broad by folder and by website,
  narrow by command.

`deny` beats `ask` beats `allow`, so a deny rule cannot carry exceptions.

## `CLAUDE.md`

Standing instructions Claude reads at the start of every session. Put these
sections in `~/.claude/CLAUDE.md` to apply them to all your work, or in a
`CLAUDE.md` at the top of a project to apply them there only.

If you already have a `CLAUDE.md`, add these sections to it rather than
replacing the file.

Changes take effect in a **new** session, not the one you are in.
