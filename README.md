# Setting up Claude Code for research work

*A guide for people who are not programmers.*

So you are about to start using Claude Code — for your own research, or for a job in someone's lab. This is how to set it up before you begin, so that it stays useful and does not quietly become dangerous. It also works if you have already been using Claude Code and want to streamline what you have.

It takes about half an hour. You do not need to know how to program: most of it is pasting a request to Claude and reading what it proposes.

## Two notes before you start

**This is about Claude Code**, the version of Claude that runs in a terminal window and can read and change files on your own machine. Not the website, not the phone app. Those cannot touch your files, which is why they never ask permission and why none of this applies to them.

**The examples are written for Linux.** The rules you end up with work the same on every system, but the commands and paths do not. On a Mac everything here works except that your home folder is `/Users/yourname` rather than `/home/yourname`. On Windows the rules are identical but the terminal commands differ — paste the step to Claude and ask it to translate.

---

## Why any of this is necessary

Because Claude Code works on your real files, it stops and asks before doing certain things:

```
Claude wants to run:

pdftotext /home/me/thesis/sources/mitchell1985.pdf -

Do you want to allow this? 1. Yes 2. Yes, and don't ask
again for commands like this 3. No, and tell Claude what to
do instead
```

Option 2 looks like the sensible answer, but it is the trap, and it is worth understanding why before you begin, because otherwise you will spend a year building the wrong thing.

When you choose it, Claude Code saves the **exact command** — every word and filename:

```json
"Bash(pdftotext /home/me/thesis/sources/mitchell1985.pdf -)"
```

Tomorrow you are reading a different PDF, so that rule cannot match. You are asked again. You approve again. Another dead rule is written.

The list grows every session and the interruptions never stop. On my own machine it reached 654 saved rules, of which 550 could never match anything again — while 48 deliberately written rules did all the actual work.

**The cost is not the nuisance.** The problem with this is that you will very rapidly start approving things without reading them very closely or knowing much about what they will do. And then you will approve something broad — "allow any Python command", say, which permits anything at all, for ever. The guard rail is still on screen. It no longer does anything.

Set up properly at the start and you avoid all of that.

---

## The five steps

1. **Make mistakes recoverable** — put your work in git.
2. **Block the dangerous, flag the thoughtful** — identify the things Claude should never do alone.
3. **Let Claude get on with the safe things** — where most of the interruptions go away.
4. **Keep commands in a form the rules can match** — one instruction, large effect.
5. **Run the diagnostic and clear out the rest.**

The order matters. Steps 1 and 2 are what make it reasonable to be permissive at step 3.

---

## Step 1. Make mistakes recoverable

Things will go wrong. Claude will occasionally do something you did not intend — replace a file's contents, edit thirty notes when you meant three. This is not a reason to avoid it; it is a reason to work somewhere that mistakes can be undone.

**So: do all your work inside a git repository.**

The following instructions are for setting up a project that you control: i.e. where you are working alone or where you are the project owner (e.g. a thesis, your essays, your notes, coding projects, etc.). If you are working with a pre-existing repo (e.g. a larger research project or something owned by somebody else), the same broad principles apply, but there are some differences in how you manage things. They are discussed in [If you are working in someone else's repository](#if-you-are-working-in-someone-elses-repository), below.

### What git is, for this purpose

A git repository is a folder that keeps its own history. Every time you commit, it records the state of everything in the folder, and you can return to that state later.

**It is local, and it is private.** Git runs entirely on your own machine. It needs no account and nothing leaves your computer. GitHub is a separate service you can push to if you want to, and you never have to. People confuse the two constantly, and it stops them using the single most useful safety net available.

Short of a command that erases the disk, a committed state can always be recovered. Emptied a file? Deleted thirty? Committed rubbish? All recoverable.

Just how important this is was demonstrated by my work on this very primer: a mistake by Claude while we were working erased all versions of one type of file and my .local.json.

**What happened, and why it is worth telling.** While we were drafting this very guide, Claude ran a command that had an unintended side effect: it overwrote two of my Claude Code settings files, replacing both with empty ones. One held the fifty-odd rules I had deliberately built up over months. The other held six hundred and fifty-four saved approvals — the accumulated junk this guide is about.

The deliberate file was recovered, but only by luck: Claude had read its contents into our conversation twenty minutes earlier, so the text still existed somewhere. The accumulated file was simply gone. There was no backup, and the folder it lived in was not a git repository. Nothing about the command looked dangerous, and it was approved the way you approve dozens of things an hour.

The part worth sitting with is the timing. We had spent the previous hour establishing exactly this: that the danger is not the obviously reckless command but the routine one, and that the only reliable protection is being able to undo things. Then it happened to us, an hour later, in the middle of writing the guide that says so.

Knowing about a risk does not protect you from it. Being able to reverse it does. Had that folder been a git repository, the whole episode would have been thirty seconds and one command, and I would not be telling you about it.

### Setting up your project-level Git repo

The basic goal is to establish a git repository for every project you work on: e.g. a thesis, each essay, different coding projects. If you do this, and commit changes automatically as you work, then most mistakes that occur while you are using Claude Code (whether by you or by Claude) can be undone. 

```
~/projects/
├── thesis/              ← its own repository
│   ├── .git/
│   ├── .claude/         ← its own permission rules
│   └── chapters/
├── fieldwork/           ← its own repository
│   ├── .git/
│   ├── .claude/
│   └── transcripts/
└── teaching/            ← its own repository
    ├── .git/
    └── .claude/
```

You might be tempted to put all your projects in a single repository. But this doesn't work. Git does not record the contents of a repository nested inside another one; it stores a single pointer instead. So your top-level repository will still *report* that something changed; but it won't record the actual changes, leaving you nothing to restore, but well aware of what you have lost. Keep projects as siblings.

If you have never used git, tell Claude and let it walk you through:

```
I have never used git. Set up a local git repository in
/home/me/thesis. Do not create anything on GitHub and do not
add a remote. Explain each command before you run it, then
make a first commit of everything currently there — but
before that first commit, check the folder for API keys,
passwords, or confidential participant data and set up a
.gitignore so they stay out. Finally, show me the one
command I would type to throw away my changes and go back to
that commit.
```

Or do it yourself. Three commands you run once, ever:

```
git --version
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

The name and email are stamped on each commit so the history says who did what. They are stored on your machine; nothing is registered anywhere.

Then, inside the project folder, create the repository:

```
git init
```

The folder is now a repository, but nothing has been recorded in it yet. Deal with credentials before you commit for the first time — that is the next section — and then make the first commit.

### Keep credentials and confidential material out

If you have followed this in order, you have a repository and nothing committed. That is exactly the right moment for this. If you already have a repository with commits in it, do this now anyway.

**Why it matters more than it looks.** Git keeps history. If a password or key is committed today and you delete the file tomorrow, the key is still there, in the history, recoverable by anyone who has the repository. Deleting it afterwards does not undo it. And if you ever push to GitHub, automated scanners find exposed keys within minutes.

Two kinds of thing to keep out:

- **Credentials** — API keys and tokens (Zotero, Zenodo, cloud storage, transcription services), `.env` files, `credentials.json`, SSH private keys, and any note to yourself with a login or password in it.
- **Confidential research material** — interview recordings and transcripts with names in them, participant data, anything covered by your ethics approval, anything a colleague sent you in confidence.

The second is easy to forget precisely because it does not look technical. For humanities work it is usually the more serious of the two.

Ask Claude to check and set up the exclusions:

```
Before we commit anything: check this folder for credentials
and confidential material — API keys, tokens, .env files,
credentials.json, SSH private keys, passwords written into
notes, and any participant or interview data. List what you
find, without printing the contents. Then create or update a
.gitignore so those files are never committed, and show me
the .gitignore.
```

A `.gitignore` is simply a list of things git should pretend not to see. Anything named in it stays on your disk and out of the history.

Then make it a habit rather than a one-off:

```
Add this to my ~/.claude/CLAUDE.md, changing nothing else in
it:

Before committing anything to git, check what is about to be
committed for credentials, API keys, tokens, passwords, and
confidential participant data. If you find any, stop and
tell me instead of committing.
```

**If something has already gone in**, do not just delete the file and commit that — the history still holds it. Assume the key is compromised: revoke it and issue a new one. That takes five minutes and is certain. Rewriting git history is possible but fiddly, easy to get wrong, and does nothing about copies already pushed or cloned. For confidential material rather than a key, ask for help rather than improvising.

### Make the first commit

Now that nothing private is going in:

```
git add -A
git commit -m "starting point"
```

That is it. The folder has a history, and everything from here on can be undone.

### Using it

Two things to be able to do, and no more for now. Both are things you can simply ask for.

**Save a restore point**, before anything you are unsure about:

```
Commit everything as it stands now, so I have a restore
point to come back to.
```

**Go back to it**, when something has gone wrong:

```
Something has gone wrong. Show me what has changed since my
last commit, then throw those changes away and put the
folder back how it was.
```

Asking for the changes first is worth the extra second: it lets you see the damage before you discard it, and occasionally you will find that some of it was work you wanted to keep.

**The two commands underneath.**

Those requests run these:

```
git add -A && git commit -m "before the big tidy-up"
```

```
git checkout -- .
```

**Write the second one down somewhere you can find it** — on paper, or in a note that is not inside the folder in question.

You will nearly always use the plain-English version. But the undo is the one thing you want to be able to do without help: when Claude has just made the mess, or when you have closed the session and something looks wrong, being able to type one short command yourself is the difference between a moment's annoyance and an evening lost. It is the only command in this guide worth memorising.

### Two cautions

**Make committing-before-bulk-work automatic.** A restore point created after the damage is no use, and remembering to ask every time is exactly the sort of discipline that fails on a busy afternoon. So do not rely on remembering. Tell Claude to do it always:

```
Add this to my ~/.claude/CLAUDE.md, creating the file if it
does not exist and changing nothing else in it:

Before any batch or multi-file operation — a cleanup pass
over many files, a rename or move batch, a scripted edit —
commit anything outstanding first, without being asked, as a
restore point. Say which commit you made. If the files are
not under git at all, say so before starting rather than
after.

Then show me what you wrote.
```

That last sentence matters as much as the rest. It means that if you ever point Claude at a folder you forgot to put under git, you are told *before* the bulk edit rather than discovering it afterwards.

Step 4 sets up the rest of this file. If you do that step later and it offers to add a commit line, you already have one — leave it alone.

*A note for later, not a problem now.* After months of heavy use, snapshot commits can come to outnumber real ones — in my own vault they were 33 of the last 41. This is untidy rather than dangerous, and the snapshots are doing exactly what they are there for. If the log ever gets hard to read, filter them out rather than making fewer of them:

```
git log --oneline --invert-grep --grep="^Snapshot"
```

That works as long as snapshot messages stay consistent, which is why the instruction above gives them all the same wording.

**Git is for text, not for big media.** It handles prose, notes, code, and data tables beautifully. A folder of manuscript photographs or interview audio will make the repository enormous and slow. Keep large media in a separate folder *outside* the repository — and then make sure it is genuinely protected, because git is no longer doing it for you:

- **Two copies at least, one of them not on this machine.** Your university almost certainly gives you cloud storage with a large quota; that is the easiest second copy and it keeps old versions. An external drive works too — on Ubuntu, Déjà Dup (installed as "Backups") will do it on a schedule without your thinking about it.
- **Syncing is not backing up.** Dropbox and OneDrive copy a deletion as faithfully as they copy a file. A synced folder is one copy, not two.
- **Irreplaceable primary material deserves a repository**, not just a backup — Zenodo, or your institution's data repository. That is preservation rather than protection, it gives the material a DOI you can cite, and it is increasingly what funders expect anyway.
- **Put an ask rule on that folder** once you have set one up, so nothing changes there without your seeing it first. See *the ask list* below.

## Step 2. Block the dangerous, flag the thoughtful

Now that your work can be recovered, the next step is to block the things that recovery would not save you from.

There are three levels, and it is worth knowing all three because you will see them in your settings file:

|           | What it does                             |
| --------- | ---------------------------------------- |
| **deny**  | refuses outright — Claude cannot do this |
| **ask**   | stops and checks with you every time     |
| **allow** | goes ahead without asking                |

**deny** is for commands that neither you nor Claude should run without serious thought. `rm -rf *` deletes everything below where it is run, with no warning and no undo — and git cannot help if the repository itself goes with it. `sudo` runs as system administrator, outside anything git is watching. A downloaded file piped straight into a shell runs code you have never seen.

**ask** is for the level below: commands that are fine in themselves but should never happen while you are not thinking. Anything that rewrites history or sends work outside your machine belongs here.

This list needs no experience and no knowledge of your project. It is the same for everybody, so you can set it on your first day:

```
Add deny and ask lists to .claude/settings.json in my
project. Create the file if it does not exist and leave
anything already in it alone.

Deny: sudo, rm -rf, and piping a download into a shell.

Ask: git push, git reset --hard, git checkout with a branch
name, and anything that deletes or moves files in bulk.

Show me the file afterwards and explain in one line what
each rule does.
```

Two things worth understanding.

**A denied command is not made impossible.** You can still run it yourself in a terminal, deliberately, having thought about it. The rule only removes the chance of it happening while your attention is elsewhere — which is the failure this whole guide exists to prevent.

**"deny" beats "allow".** So if you have already been using Claude Code and clicking "don't ask again", this step immediately neutralises anything over-broad you approved on a tired afternoon, without waiting for the clear-out at step 5.

## Step 3. Let Claude get on with the safe things

This is where the interruptions actually go away, and — because of steps 1 and 2 — it is now safe to be generous.

Most of what Claude does for you needs no supervision, particularly when a restore point exists. Reading your own files. Making edits you asked for. Looking up a DOI. Pulling text out of a PDF you are citing.

Two kinds of permission cover nearly all of it, and both are broad in a way that is fine:

- **Folders** — one rule covers every file in your project, including files that do not exist yet.
- **Websites** — one rule per site you look things up on.

```
Add these to permissions.allow in .claude/settings.json,
keeping everything already there:

- reading and editing any file under /home/me/thesis
- fetching pages from doi.org, api.crossref.org,
  openalex.org, and
archive.org  [replace with the sites you actually use]
- running pdftotext and pdfinfo

Show me the result and explain each line in plain English.
```

The rules will look like this:

```json
"Read(/home/me/thesis/**)"
"Edit(/home/me/thesis/**)"
"WebFetch(domain:doi.org)"
"Bash(pdftotext *)"
```

`**` means "and everything inside, however deep". So one line covers your whole project, permanently. That is why this step removes so much of the asking, and why nothing else in this guide needs to be broad.

Things worth allowing, for typical humanities work: reading and editing your project folder; the bibliographic sites you use — Crossref, OpenAlex, DOI resolution, the Internet Archive, HathiTrust, your library catalogue; `pdftotext` and `pdfinfo` for working with sources; `exiftool -s3` for reading file metadata.

Things to leave out, however tempting: anything that runs a programming language, anything that deletes, and anything touching folders outside the project. See *Never allow these* below.

## Step 4. Keep commands in a form your rules can match

Claude Code already runs a built-in set of read-only commands without asking, in every mode: `ls`, `cat`, `echo`, `pwd`, `head`, `tail`, `grep`, `find`, `wc`, `which`, `diff`, `stat`, `du`, `cd`, and the read-only forms of `git`. That set is not configurable, and you never need a rule for any of it. A large part of a typical saved list is rules for these, which do nothing at all.

It is also cleverer about combinations than you might expect: `cd subfolder && ls` runs without a prompt, because each half qualifies on its own.

What that built-in handling does *not* extend to is the rules you write yourself. **Those match the literal text of the command.** So a rule like:

```json
"Bash(pdftotext *)"
```

matches `pdftotext sources/mitchell.pdf -` and does not match `cd sources && pdftotext mitchell.pdf -`, because as a piece of text that is not a `pdftotext` command. It begins with `cd`.

Some compounds also prompt even when both halves look harmless:

- **`cd` together with `git`**, when the `cd` moves to a different folder — because running `git` somewhere new can execute that folder's hooks.
- **Unquoted wildcards with commands that have write-capable options** — `find`, `sort`, `sed`, `git` — because the wildcard could expand into something like `-delete`.

So it is worth asking Claude to write commands in a predictable form. Paste this:

```
Add a "Command style" section to my ~/.claude/CLAUDE.md.
Create the file if it does not exist and change nothing else
in it. It should tell you to:

- avoid starting a command with cd; use the full path
  instead
- use a tool's own option when a folder is needed, like git
  -C /path/to/repo
- keep to one command per call rather than chaining them
- prefer your Read, Edit, Grep and Glob tools over cat, sed,
  grep and find
- commit anything outstanding before starting bulk work, so
  I have a
restore point  (skip this line if you already added it at
step 1)

Then show me what you wrote.
```

`CLAUDE.md` is a plain file of standing instructions that Claude reads at the start of every session, so you write something once instead of repeating it. The one in `~/.claude/` applies to all your work. **It only takes effect in a new session** — quit, restart, and ask *what does my CLAUDE.md say about command style?* If it can tell you, it is working.

The fourth line matters more than it looks. Permissions for Claude's own file tools are based on **folders**; permissions for terminal commands are based on **exact wording**. A folder rule keeps working forever, including on files that do not exist yet. So the less of your work goes through the terminal, the less of any of this you ever have to manage.

## Step 5. Run the diagnostic and clear out the rest

Now that the sensible rules exist, Claude's own diagnostic can tell you what is genuinely left over. In Claude Code:

```
/fewer-permission-prompts
```

It reads your recent sessions, finds what keeps coming up, and proposes rules for the harmless ones.

**Read the proposal rather than accepting it.** Two checks:

- Does it want to allow a **programming language or a shell** — python, node, bash, ruby, perl, awk? Refuse. Allowing one permits anything at all and makes every other rule pointless.
- Does it want to allow something that **changes or deletes** rather than only reading? Refuse.

Do not be disappointed by a short list. On my own machine this produced only fifteen rules, because the folder and website rules had already absorbed nearly everything and most of the remainder should never be allowed.

**If you have been using Claude Code for a while**, there is a clear-out to do as well. Your saved approvals live in `.claude/settings.local.json` — that is the file that fills with junk, separate from `.claude/settings.json`, which holds the rules you have just written. Claude Code reads both and adds them together, so you can throw the first away without losing the second:

```
Back up .claude/settings.local.json to my home
folder, then empty its permissions.allow list,
leaving the file valid. Do the same for
~/.claude/settings.local.json if it exists.
Do not touch .claude/settings.json. Then add
.claude/settings.local.json to .gitignore.
Show me both files afterwards.
```

Nothing breaks. Anything you genuinely need will ask once more — and this time you add a proper rule, because from now on you never choose "don't ask again".

---

## If you are working in someone else's repository

A research job usually means working in a repository belonging to your supervisor, the lab, or a funded project. Everything above still applies. Four things change, and they are in this order because each one makes the next one safe.

**1. Work on your own branch — and first, work out whether you need a fork.**

Two situations, and it is worth asking rather than guessing:

- **You have been given write access** to the repository. Clone it directly.
- **You have not.** Make a *fork* — your own copy of the whole repository under your own account — and work in that.

To clone directly:

```
git clone https://github.com/theirname/theproject.git
```

To fork and clone in one step, if you have GitHub's `gh` command:

```
gh repo fork theirname/theproject --clone
```

Or use the **Fork** button on the repository's GitHub page, then clone the copy that appears under your own account. Either way, add the original so you can pull in their later changes:

```
git remote add upstream https://github.com/theirname/theproject.git
```

Then, before you do anything at all, make a branch:

```
git checkout -b yourname/what-you-are-doing
```

Never work on `main`. A branch costs nothing, keeps your work separate from everyone else's, and means a mistake stays yours.

Pushing is a separate decision from committing. Local commits are your safety net and cost the project nothing; pushing puts your work into a history other people rely on. Ask before the first one:

```
Add these to permissions.deny in
.claude/settings.local.json: git push,
git push --force, and git reset --hard.
```

Denying `git push` outright is reasonable here, even though the main guide only asks about it. You can still push yourself, deliberately, once it has been agreed.

**2. Check that your credentials are excluded — before your first commit.**

This is the one thing on the list that cannot be undone. Their `.gitignore` was written for their setup, not yours: it will not know about your API token, your `.env` file, or the interview transcript you dropped in the folder to work on.

```
This is someone else's repository. Check whether anything in
my working folder would be committed that shouldn't be — API
keys, tokens, .env files, credentials, or confidential
participant data. List what you find without printing the
contents, and tell me whether the existing .gitignore
already covers it.
```

If something needs excluding and the `.gitignore` is theirs, do not simply edit it — that is a change to their project. Use `.git/info/exclude` instead, which does the same job for you alone and is never committed. Ask Claude to put it there.

**3. Keep your settings separate from the repository's.**

`.claude/settings.json` is a shared file. Adding it changes how Claude behaves for everyone who clones the project, and that is not your call to make.

Put your own rules in one of two other places: `.claude/settings.local.json`, which is personal to you and not committed, or `~/.claude/settings.json`, which applies to all your work everywhere. If you think the project genuinely needs shared rules, propose it rather than committing it.

This reverses the advice at step 5, so it is worth being clear about why. In your own project, `settings.local.json` is where "don't ask again" dumps its junk, and you empty it because your real rules belong in `settings.json`. Here you cannot use `settings.json` — it is theirs. So `settings.local.json` becomes the place where your deliberate rules live, and you write them there on purpose rather than letting them accumulate. Same file, opposite job.

**4. Keep your snapshots separate.**

In your own project a noisy history is untidy. In someone else's it is discourteous and makes their log hard to read.

Your snapshot commits stay on your branch, which step 1 has already taken care of. Before you offer the work — as a pull request, or for merging — ask for a tidy-up:

```
Show me which commits on this branch are snapshots rather
than real work, and propose how to combine them into a small
number of meaningful commits. Do not change anything until I
have looked at the proposal.
```

---

One further point, less technical and more important than any of it. In a shared repository the cost of a mistake is not only yours. Be more conservative than you would be working alone: keep the ask list longer, keep the allow rules narrower, and when something looks destructive, ask a person rather than approving it.

## The ask list, later

Everything above can be done on day one. This part cannot, because it depends on knowing what you actually do.

Step 2 used **ask** for dangerous commands. It is also useful for dangerous *places* — a folder where you want to be told before anything changes.

The usual advice is "ask before doing something that has gone wrong before", which is no use when you are starting out and nothing has gone wrong yet. 

But even whe you are starting out, you can see one place where the danger might lie: files you can't afford to lose. So if you don't have a list of things that have gone wrong, ask yourself this instead:

> **Where is the only copy of something I could not make again?**

Interview recordings. Photographs of a manuscript you visited once. A transcription that took a fortnight. Anything where losing the file means losing the work rather than repeating it.

```
Add an ask rule to .claude/settings.json for any
edit or write inside /home/me/thesis/interviews.
Leave everything else alone.
```

A second question, if your work sits in Dropbox or OneDrive: **does deleting here delete everywhere?** If so, that folder deserves an ask rule, because sync will faithfully copy a mistake to every device you own before you notice.

Then, every few months, the retrospective question becomes useful:

> **What has actually cost me work?**

Not what sounds dangerous. What has genuinely bitten. The answer is usually short and rarely what a cautious person would have guessed. For me it turned out to be bulk tidying of my own notes — a batch edit that emptied several files. Not the internet access, not anything exotic. Editing his own prose, many files at once.

When something goes wrong, add a rule rather than resolving to be more careful. The rule still works in six months.

## Reading a rule

You will be reviewing what Claude proposes, so it helps to recognise the shapes.

| Rule | Means |
|---|---|
| `Read(/home/me/thesis/**)` | may read any file in that folder or below |
| `Edit(/home/me/thesis/**)` | may change those files |
| `WebFetch(domain:doi.org)` | may fetch pages from that site |
| `Bash(pdftotext *)` | may run that one program, with any options |
| `Bash(git status)` | may run exactly that, nothing else |

`*` means "anything can follow". `**` means "any folder, however deep".

The asymmetry to remember: **folder rules are broad and safe**, because they are confined to a place you chose. **Command rules get dangerous as they widen**, because a single program can do many different things. Hence generous with folders, mean with commands.

## Never allow these

**Languages and shells** — `python`, `python3`, `node`, `bash`, `sh`, `ruby`, `perl`, `awk`. Each can run any instruction whatsoever, so allowing one makes every other rule decorative. This is the single mistake worth avoiding in the whole guide.

**`curl` on its own** — fine for fetching a page you name, but a blanket rule also permits sending your files somewhere. Use `WebFetch` rules instead.

**`git push`, `rm`, `sudo`** — these reach outside the folder you are working in, or outside what git is protecting.

If you catch yourself wanting one of these to stop an interruption, that is the fatigue talking. Go back to step 3 and widen a folder rule instead.

## Four things that catch people out

**Many commands never ask anyway.** The built-in read-only set described at step 4 needs no rules, and rules written for it do nothing. This is a large share of what accumulates in a saved list.

**Rules from different files combine; they do not replace each other.** Your account file, the project's shared file and your local file all contribute, and Claude Code merges the permission lists rather than picking one. So a rule does not stop working because another file also has rules.

**deny beats ask beats allow, and specificity does not rescue you.** A broad `deny` blocks a call even when a narrower `allow` also matches it, and a matching `ask` prompts even when a more specific `allow` matches. This is what makes step 2 effective immediately: your deny rules override anything over-broad already sitting in your saved list. It also means a deny rule cannot carry exceptions — if you deny something, deny it completely or not at all.

**"Don't ask again" behaves differently for commands and for edits.** For a Bash command it saves a permanent rule, in `.claude/settings.local.json` at the root of your git repository, and it applies across that whole repository in future sessions. For a file edit it is not saved at all — that approval lasts until you close the session.

## What this will not fix

None of this changes how often Claude pauses to *check in* — "I've finished the first section, shall I go on?" That is a different interruption from a permission prompt, and the two are worth keeping apart when you describe the problem to someone. Nothing here makes it happen less.

## Checklist

1. Project in a local git repository.
2. A `.gitignore` for keys, passwords, and confidential data — set up *before* the first commit.
3. A first commit made, and `git checkout -- .` written down somewhere as your undo.
4. Deny and ask lists — the same ones everybody needs.
5. Folder rules for your project, website rules for your sources.
6. Standing instructions in `~/.claude/CLAUDE.md`: command style, commit before bulk work, check for secrets before committing.
7. `/fewer-permission-prompts`, reviewed rather than accepted wholesale.
8. `settings.local.json` emptied and gitignored, if you had one.
9. An ask rule on wherever the only copy of something irreplaceable lives.
10. Never choose "don't ask again".

---

## About this guide

**Checked against Claude Code as of 24 August 2026.** The permission system changes: the behaviour described here — the built-in read-only command set, how rules from different files combine, and the deny/ask/allow precedence — was verified against Anthropic's documentation on that date. If something does not behave as described, the documentation is the authority, and please open an issue.

The figures come from my own machine and my own sessions over the months before that date. They are one person's usage, offered as illustration rather than as a measurement of anything general.

**How this was written.** Drafted by Claude (Opus 5) in Claude Code, directed and revised by me across a working session, with the structure, the ordering of the steps, and several of the corrections mine. The factual claims about Claude Code's behaviour were checked against the published documentation rather than taken from the model. Errors that remain are mine.

**Licence.** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Use it, adapt it for your own lab, translate it; please keep the attribution.

Daniel Paul O'Donnell, University of Lethbridge · [0000-0002-0127-4893](https://orcid.org/0000-0002-0127-4893)
