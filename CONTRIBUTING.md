# Contributing to xymon-facelift

Patches are welcome. This file holds the rules every change here meets. They
are adapted from the Xymon rules, so working on both feels the same.

Fork, remotes and the pull-request flow are the same for every repository in
the organisation and are documented once, in the wiki: start with
[first-contribution.md](https://github.com/xymon-monitoring/xymon-wiki/blob/main/docs/contributing/git/first-contribution.md);
[git-setup.md](https://github.com/xymon-monitoring/xymon-wiki/blob/main/docs/contributing/git/git-setup.md)
has the remote configuration.

Discussion happens in the issues of this repository.

## Scope

- **Every change complies with the spec**, [SPEC.md](SPEC.md): the facelift
  rules, from the high-level goals down to the low-level rules and the tests
  that enforce them. A rule that has a test is checked by it; a pull request
  that breaks one does not go in. A change that needs a rule to change
  changes `SPEC.md` first, in a pull request of its own. `SPEC.md` also says
  which changes belong in the Xymon repository instead.

## Planning the work

From high level to low level:

- **Wide first, at the top.** List all the parts before going deep into any,
  and decide for each: now, later, or dropped. Stop going wide after one or
  two levels.
- **Then deep, one branch at a time.** Split the chosen branch until each piece
  is one pull request, as the next section defines it, and finish that branch
  before opening another. Start with the branch the others depend on.
- **Park what is not for now** as an issue with the `later` label, so it is
  neither lost nor discussed again each time.
- **One issue per part**, sub-issues for its pieces; each piece that cannot be
  split further is closed by one pull request.

## Small, well-defined changes

**As small as possible.** When a change can be split, split it, but never
separate a feature from its test.

Pull request:

1. **One purpose.** You can describe it in one sentence without "and".
   If a reviewer could accept one part and refuse another, split it.
2. **Nothing extra.** No cleanup, renaming or reformatting the purpose does
   not need; those are pull requests of their own.

Commit:

3. **One step.** A pull request may hold several commits, each one step, such
   as a refactoring and then the change that needs it.
4. **Works on its own.** Each commit builds and passes the tests. The commit
   that adds a behaviour carries its test, so it fails without it.
5. **Explains itself.** The message says what changed and why, so that someone
   reading `git log` later understands it without the pull request.
6. **Corrections are folded in.** A correction found in review goes into the
   commit it corrects, not into a new commit: a pull request may land with a
   merge commit, and then every commit reaches `main` as it stands.

For example, a new skin, a new layout and a fix to the Look and feel page are
three pull requests.

## Pull requests

**Who merges.** Stef Coene, who maintains this repository, may push directly
or merge their own pull requests without a review. Nobody else may: every
other pull request is merged by the maintainer, so each change is understood
before it goes in. A ruleset on `main` enforces this.

**Who is the author.** Whoever opens the pull request is its author, whatever
helped them write it: they have read every line, can say what each part does
and how it was checked, and answer for it. Responsibility does not move to a
tool.

- **Say what you verified, and how.** "Ran the tests and opened the page in
  Firefox" is useful; "should work" is not. What you could not test, say too:
  it is not held against you, and it tells the reviewer where to look.
- **Test each behaviour.** Each visible behaviour a change makes true gets a
  test that fails without it. Check that by breaking the behaviour, and say
  what you broke. Test the behaviour, not its spelling. For a behaviour that
  stays untested, say why and what was checked instead.
- **Keep the description accurate** as the pull request evolves: a reviewer
  reading it after three force-pushes should not be reading the original plan.

**Title.** The component and a colon (`skins:`, `layouts:`, `lookandfeel:`,
`xymonsettings:`, `docs:`, `tests:`), then a complete sentence in lower case
that says what the change makes true, in 80 characters or fewer. When one
behaviour replaces another, name both. "Fix" and "add" rarely fit: the diff
already shows the kind of change. No `DRAFT:`; GitHub has a draft flag. A
commit subject follows the same rule.

**Description.** What is wrong or missing first, then what changes, so that
two sentences explain why the pull request exists. Then what the diff cannot
show: an order against another pull request, something left alone on purpose,
something not tested. Cite what can be checked: a file and line, a pull
request number. A dependency goes on a line of its own, `Depends-on: #N`;
`Fixes #N` goes here, not in the title.

**Pre-review.** Encouraged, not required: a table of what was checked, how,
and the result.

- Where a change can be seen in a browser, it starts with a **before** and an
  **after** a reviewer can repeat, with a screenshot of each, and names the
  browsers looked at.
- Then a row for each block of changed lines: the claim it settles, the test
  or other check, and whose run it was.
- It ends with what was **not checked**, and why, and what is **left open for
  the approver**: a judgement the author cannot make.
- A result an AI tool reports counts only once a person has rerun it.

**Comments.** Not a second copy of the description. A comment answers a
finding, says what a revision changed, or records a decision taken since.
When a fact changes, the description is updated.

## Reviews

An approving review says the change makes the facelift better as it stands,
not that it is perfect. The reviewer looks at the whole change at the head
they approve, and says:

- which claims of the pre-review they verified, by running a check, and which
  they only read;
- what they found, or that they found nothing, and what they could not check.

A review of the text reports facts: a sentence that is false, misleading, or
in conflict with a rule. A preference is not a finding. When the same choice
keeps coming back, the author decides it once, records it with the rejected
alternative in the pull request or issue, and later rounds take it as given.

## Where things get written down

**One place for each fact, in the whole repository.** Every kind of file can
carry documentation: Markdown, code and its comments, stylesheets, scripts and
their help text, HTML pages, configuration samples, tests. A fact is written
once, where its reader is, and every other place points to it. When one fact
sits in two places, one copy goes stale and nothing says which.

Copy a fact only when its second reader cannot reach the first copy: an agent
does not open a link, so `AGENTS.md` repeats the Xymon rules it relies on; a
test that checks a message is a copy that fails when the message changes.

| Place | Who reads it | What it carries |
|---|---|---|
| pull request title | everyone scanning the list | the component, then what the change makes true |
| pull request description | the reviewer deciding | the problem, the change, what the diff cannot show, the evidence |
| pull request comments | the reviewer, in order | answers to findings, what a revision changed |
| commit message | whoever reads `git log` years later | what changed and why, standing alone |
| code comment | whoever edits that line | why this and not the obvious alternative |
| test header and messages | whoever the test fails on | what it checks, what was expected and what happened |
| `SPEC.md` | whoever designs, changes or reviews the facelift | the rules, from high level to low level, and the tests that enforce them |
| `README` and docs | an administrator installing the facelift | what it does and how to switch it on and off |
| issue | whoever decides | the problem, the proposal, what is out of scope |

A decision splits across these places: its visible effect goes in the docs,
why in the commit message, why not the obvious alternative in a code comment,
and what was weighed and rejected in the issue or pull request that decided it.

A maintained document carries approximations that stay true ("nearly all",
"none"); exact figures belong in a pull request, an issue or a commit message,
which are read once.

## Copyright and licensing

This repository is under the GNU General Public License version 2, the same as
Xymon; see [LICENSE](LICENSE). Copyright in a contribution stays with its
author. Make sure you have the right to submit it under that licence, and keep
existing copyright and licence notices.

Do not add an icon set, a font, a stylesheet or a script made by someone else
without checking that its licence is compatible, and keep its notices with it.

## Before asking for a review

Read the text your change adds once more, and again whenever it changes:

1. **True:** each sentence against what it describes.
2. **Current:** commit ids, counts and citations against the final head.
3. **In its place:** each fact once, where its reader is.
4. **Said:** what stays untested is in the description.
5. **Readable:** one idea per sentence.
6. **Short:** last, and without losing anything a reader needs. Before each
   cut, name what a reader could no longer do: if it is only "read it twice",
   cut; if it is "check the claim", "understand why" or "know whether it
   affects them", keep it.

## Style

Match the file you are editing.
