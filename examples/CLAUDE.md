## Command style

- Avoid starting a command with `cd`; use the full path instead.
- Use a tool's own option when a folder is needed, like `git -C /path/to/repo`.
- Keep to one command per call rather than chaining them.
- Prefer your Read, Edit, Grep and Glob tools over `cat`, `sed`, `grep` and `find`.

## Before committing

- Before any batch or multi-file operation — a cleanup pass over many files, a rename or move batch, a scripted edit — commit anything outstanding first, without being asked, as a restore point. Say which commit you made. If the files are not under git at all, say so before starting rather than after.
- Before committing anything to git, check what is about to be committed for credentials, API keys, tokens, passwords, and confidential participant data. If you find any, stop and tell me instead of committing.
