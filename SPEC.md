# xymon-facelift specification

The rules for the facelift, from the goal down to each rule and the test that
enforces it. A rule changes through a pull request to this file; the
discussion behind it is in issue #1.

## Goal

A facelift of the Xymon web interface: it changes how the pages look, not what
they show or how they work. It is not a redesign. A redesign would first need
an analysis of what users need and of what other monitoring tools do; that is
out of scope here.

## Principles

- **Xymon's default does not change.** Without a setting from this project, the
  pages Xymon generates are byte for byte what they are today.
- **Everything is opt-in.** An administrator installs the facelift, switches each
  part on per installation, and can switch it off again.
- **Xymon itself gets only small changes, off by default**, each as its own pull
  request in the Xymon repository.
- **Every rule is enforced by a test**, so a change that breaks a rule fails at
  once.

## Scope

In scope:

- **Skins:** icon sets beside the classic GIFs, selected with `XYMONSKIN`.
- **Layouts:** stylesheets and scripts that restyle the pages, loaded through
  `XYMONBODYFOOTER`. This includes moving the info, trends and clientlog
  columns, which are not tests, to the start or the end, or merging them.
- **The Look and feel page:** previews a layout and a skin on a real Xymon page,
  and shows the commands that make the choice permanent.
- **xymonsettings.sh:** shows and changes `xymonserver.cfg` settings, and
  switches skins and layouts.
- **In Xymon, off by default:** the page refresh as a setting, and icon classes.

Parked for later (label `later`):

- **Text icons**, drawing each status icon as a character. Proposed so that a
  page can be copied into an LLM for analysis. For that use case, the status
  board (`xymondboard`) is the better source; a read-only API or MCP server on
  top of it would be a separate project. If text icons return, they come as a
  separate opt-in, and rule 9 applies to them.

Out of scope:

- What the pages show (hosts, tests, colours) and how the CGIs work.
- A redesign of the interface; see Goal.

## Rules

Each rule names the test that enforces it and where that test lives. "Not
written yet" means the rule holds by review until its test exists.

### Xymon itself (tests in the Xymon repository)

**1. The default output does not change.**
With the default configuration, the pages Xymon generates are byte for byte
the same as before the change. First every page that goes through the code a
pull request changes: the pages that write status icons, and the headers with
the META refresh. The rest (snapshot, reports, WML, RSS) is added step by step.
Test: render the pages from a fixed status board, once with the pull request's
base commit and once with its head, and compare them after masking date, time
and version. The reference is generated in the test, not stored in the tree.
Starting point: the shared status board in `tests/lib/xymongen-board.sh`
(xymon-monitoring/xymon#605). Not written yet.

**2. The META refresh stays.**
Every page that refreshes today carries exactly the same
`<META HTTP-EQUIV="REFRESH" ...>` line with the default configuration.
Test: check that line in each refreshing page. Not written yet.

**3. No extra files are loaded by default.**
With the default configuration, a page references no stylesheet, script or
image that it did not reference before.
Test: compare the `href` and `src` references with the reference output of
rule 1. Not written yet.

**4. Every new setting is off by default.**
Its default reproduces today's output, and it is documented in
`xymonserver.cfg(5)`. The planned settings: the page refresh, whose default is
today's META line, and opt-in icon classes.
Test: rules 1 to 3 cover the default; each setting also gets a test of what it
does when switched on. Not written yet.

### The facelift (tests in this repository)

**5. No Xymon template changes.**
Layouts are loaded through `XYMONBODYFOOTER`, skins through `XYMONSKIN`. The
facelift does not edit or replace a Xymon header or footer template.
Test: install the facelift into a scratch Xymon tree and check that no file
Xymon installed changed. Not written yet.

**6. Opt-in and reversible.**
Installing the facelift changes nothing visible until a part is switched on. It
never overwrites a file Xymon ships; a setting in `xymonserver.cfg` is changed
only as rule 12 says. Switching a part off brings back the default pages
unchanged.
Test: install, render, switch on and off again, render, and compare with the
pages before installing. Not written yet.

**7. Works on a plain Xymon 4.3.x.**
Skins and layouts work without the new Xymon settings of rule 4. A layout finds
the status icons by their file name when the icon classes are off.
Test: run the layout tests against pages from an unmodified Xymon 4.3.x. Not
written yet.

**8. The page keeps refreshing when something fails.**
With a layout on, a page keeps its META refresh, unless the administrator has
chosen a script refresh through the refresh setting. Then the timer is inline in
the page, not in the layout file, with the META refresh inside `<noscript>` as a
fallback, so a layout file that fails to load cannot stop the refresh. An inline
script blocked by a Content-Security-Policy is a known limit of that opt-in.
Test: render each refreshing page with every layout, and check for the META
refresh, or for the inline timer and the `<noscript>` fallback when the script
refresh is chosen. Not written yet.

**9. No information is lost.**
Every skin and layout shows everything today's icons show: colour,
acknowledged, recent, nopropagate, age, and dial-up and reverse tests. Today,
dial-up and reverse tests have their own icon set (`--dialupskin`,
`--reverseskin`), and nopropagate and age are in the ALT and TITLE text; every
mode keeps both.
Test: render a status board that holds each of these states with every skin
and layout, and check that each state is still shown. Not written yet.

**10. Nothing is loaded from the internet.**
No CDN, no web fonts, no external script, stylesheet or image: everything a
page loads comes from the Xymon server itself. Many Xymon servers have no
internet access, and a monitoring page must not depend on a third party.
Test: check every shipped HTML, CSS and JavaScript file for references to
another host. Not written yet.

**11. Plain ASCII files.**
Every shipped stylesheet, script and HTML file is plain ASCII. The Xymon pages
may be served without a character set, and a browser then reads them in its
own default one. A character outside ASCII is written as an escape (`\u2713`
in JavaScript and CSS, `&#x2713;` in HTML).
Test: check every shipped text file for bytes outside ASCII. Not written yet.

**12. An existing setting is added to, never replaced.**
When the facelift switches a part on, it adds to a setting the administrator
already has: a layout is added to an existing `XYMONBODYFOOTER`, not put in its
place. Switching the part off removes only what the facelift added.
Test: start from a `xymonserver.cfg` with a local `XYMONBODYFOOTER`, switch a
layout on and off, and check that the local value is kept and then restored.
Not written yet.
