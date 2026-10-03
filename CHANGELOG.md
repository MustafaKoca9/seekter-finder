# Changelog

The version is the git tag; there is no version file. Each release is also on
[the releases page](https://github.com/selfishprimate/seekter/releases) with the
same text.

## v0.2.0 — 4 October 2026

The release that changes what Seekter does on LinkedIn. Until now it filled
Easy Apply forms and searched LinkedIn through its internal API, and LinkedIn
restricts accounts for exactly that. Nothing in the kit said so. This version
stops acting on LinkedIn, makes reading it a choice you make knowingly, and
tells you what you take on before the first run.

### Seekter was automating LinkedIn, and LinkedIn restricts accounts for it

LinkedIn's help page on prohibited software does not permit browser extensions
that "scrape, modify the appearance of, or automate activity on LinkedIn's
website", and says accounts that use them risk being restricted or shut down.
Seekter drives your browser through an extension. On 0.1.x a daily run filled
Easy Apply, ran around 40 searches through LinkedIn's internal API and harvested
the notification feed, all from your own account. If you have been running it
daily, that is what LinkedIn has been seeing. The User Agreement also rules out
the obvious fallback of a second account.

Now:

- **Easy Apply is never filled, in any mode**, and no action is taken on
  LinkedIn: no saving, following, messaging or alert editing. Easy Apply
  postings come back to you in the report as a list to send yourself.
- **By default Seekter does not open LinkedIn at all** (`linkedin.mode: email`).
  It reads your LinkedIn job-alert emails in your inbox and finds each posting
  on the employer's own application system. A digest mail shows only about six
  of its matches (measured 4 Oct: "30+ new jobs" above six cards), so narrow
  alerts lose much less than broad ones.
- **Reading LinkedIn is opt-in** (`linkedin.mode: read`), and setup asks with a
  plain warning that it is against LinkedIn's terms. It is read-only and
  limited: at most 15 searches and 60 job details per run, 3 seconds apart, one
  at a time. A "too many requests" error, a security-check or login redirect, or
  an unusual-activity page stops it for the run and switches the mode back to
  `email`.

The cost is volume. On 2 Oct the searches found 352 postings and the alert
feed 63, so `email` mode on its own finds far fewer.

### Your own links come first

Finding a posting takes a person seconds; filling its form is the work. Paste
links into the chat or drop them in `profile/links.txt` (git-ignored), and they
are filled before every other source. A LinkedIn job link is fine: Seekter
finds the employer's own form behind it, or hands it back to you if it is Easy
Apply.

### Nobody was told what they were taking on

Seekter sends applications in your name, uses other sites under their terms
and runs on a paid Claude plan. None of that was written down, and "Seekter is
free" read as the whole story.

- `DISCLAIMER.md`: what you are responsible for, whose terms you follow, no
  warranty, no affiliation, and that Seekter is free but running it is not.
- `PRIVACY.md`: Seekter collects nothing, and this is where your data goes
  while it works: Anthropic, the employers you apply to, the job sources, and
  your inbox, read-only.
- `/seekter-init` now opens with one sentence covering those points and goes on
  only on a yes. The README has the same three points above Quick start.

### The references spoke one candidate's language

The kit was written during one person's job search, and twenty-odd reference
files used that person's language and country as their examples: translated
button labels, local search terms, a city spelled two ways, a dial code. A user
elsewhere would have needed their own copy of each line. Every lesson is still
there, now written to hold in any language and country, with `<CITY>`,
`<COUNTRY>` and `+<code>` where a place is needed.

### Also

Twelve form-system traps measured on 2 Oct, among them:

- Ashby can show a filled field as "Missing entry"; filling it a second way
  clears it, and the first Submit may only blur the field.
- Lever validates every `urls[...]` field as a URL, so free text in one fails
  the submit silently.
- Teamtailor's required Address can be a location search box that a script
  cannot fill.
- CleverStaff uploads the CV and then rejects the form anyway, until the file is
  tied to its field.
- Oracle Cloud HCM creates an account on its first screen, so it is handed to
  you.
- Google Forms records the signed-in Google account's email with an upload,
  which may not be the address your profile names.

### Upgrading

- The next `/seekter-run` asks you the disclaimer once. A no stops the run.
- Your profile starts in `email` mode. Create your LinkedIn job alerts with
  **Email** delivery, one per row of `linkedin.searches` in
  `profile/search.json`, narrow rather than broad. To keep reading LinkedIn
  searches yourself, set `linkedin.mode` to `read`; Easy Apply stays off either
  way.
- Nothing else to migrate. `profile/`, `applications/` and `runs/` are
  git-ignored and untouched.

## v0.1.2 — 2 October 2026

A bug-fix release. Two of the three fixes are about things the kit did without
telling you: one sent an application a step early, the other left rejections
looking like open applications.

### On a one-page Easy Apply form, "next" was Submit

The Easy Apply driver advances by taking the last non-cancel button in the
modal. On a multi-page form that is Next or Review. On a form with a single page
it is **Submit**, and the driver had no way to tell the two apart.

Measured 2 Oct: the modal opened with no page counter, the first advance sent
the application, and there was no review step. Nothing false went out, because
the contact page is checked before advancing. But the review step is also where
the pre-ticked "follow this company" box gets unticked, and that opt-in was left
on. If you use Easy Apply on 0.1.1, some of your one-page applications have
probably gone out the same way.

The driver now reads the page counter before every advance. A modal with no
counter is one page, so the next press submits, and the run reads the whole
form before deciding to.

### The blacklist never saw the company name

The aggregator list and your profile's blacklist are both lists of company
names. The filter ran them over the posting's title, description and apply
host, which do not carry the company. Measured 2 Oct: an aggregator named in the
skill's own list got through every filter and was only caught once its job page
was open. The company is now part of what the filter reads. On LinkedIn the
reliable source for it is the first `"name"` in the detail response, because
`companyDetails` often comes back as `?`.

### Some rejections did not read as rejections

`/seekter-log` finds rejections with a regex. It had no phrasing for a role
being filled or closed, and it had `not moving forward` but not `unable to move
forward`. Measured 2 Oct: one rejection used both phrasings and matched nothing.
A miss does not look like an error. It looks like an application that is still
open, so the row stays `applied` indefinitely. The list now covers the
filled/closed phrasings and `unable to move` / `unable to proceed`.

The same sweep found Outlook's `[role=option]` rows working again, four days
after they had disappeared. The inbox reference now says to probe all three list
selectors and use whichever one answers.

### Also

- `CHANGELOG.md` is new since 0.1.1. The 0.1.1 zip did not include it, so this
  is the first download that does. `/seekter-git` now covers releases too: once a
  pull request is merged, it can write the entry and publish the version.
- The README opens on a new cover image. The image it used to point at had been
  deleted, so the first line had been broken.
- Indeed: the tenth measured pass returned nothing new for the tenth time.
- Jobicy: eight tags returned zero rows from an API that was otherwise
  answering normally.
- LinkedIn: notifications against searches, measured a seventh time. 13 ids of
  63 overlapped.

### Upgrading

Nothing to migrate. Replace the files and keep your `profile/`, `applications/`
and `runs/`; they are git-ignored and are not touched.

## v0.1.1 — 1 October 2026

A bug-fix release. If you are on 0.1.0 and run `/seekter-run`, one of the five
sources has been quietly doing nothing for you, and that is what this fixes.

### LinkedIn Easy Apply never ran if your LinkedIn is not in English

The Easy Apply driver matched on the English button names: "Easy Apply", "Next",
"Review", "Submit application". On an account set to another
interface language those labels are translated, and every check failed.

It fails silently, which is what makes it expensive. There is no error. The
detection just reports that a posting has no Easy Apply button, which reads
exactly like a posting that has closed. Measured on a 250-posting sweep: the
entire Easy Apply tier was skipped, sixty candidates went unopened, and two live
postings were recorded as closed. The run's own priority order calls that tier
the last one to cut, because it costs about six calls and no upload.

The driver no longer matches button text at all. It finds the modal from its
heading and takes the last non-cancel button inside it, which works in any
language.

Four more Easy Apply mechanics were measured and written down at the same time:
the modal opens on a JS MouseEvent dispatch and on nothing else; the
work-authorization questions arrive in either order, so they have to be read
rather than answered by position; the coordinate frame can change between two
postings inside one run; and the email field is a dropdown that may hold more
than one address.

### The queue the skill promised now actually exists

`/seekter-run` told you that if a session ran short, "the queue survives into the
next run". Nothing ever wrote it down. A run that sweeps 250 postings and applies
to eight has resolved 240 apply URLs that live only in a browser tab, and the
next session starts from nothing.

The run now writes `runs/<date>/queue-A.md` before its report, one row per
unworked posting with its resolved apply URL, and says in the file that the A/B/C
letter is a sorting hint rather than a verdict.

### Also

- Ashby: a form's own country-of-residence list outranks the location in the
  posting header. A header naming four countries looks like a closed door; the
  form offering yours is the employer saying otherwise.
- Indeed: ninth measured pass, ninth with nothing new.
- LinkedIn: notifications against searches, measured a sixth time. 7 ids of 44
  overlapped. The two channels still do not substitute for each other.

### Upgrading

Nothing to migrate. Replace the files, keep your `profile/`, `applications/` and
`runs/`, which are git-ignored and untouched.

## v0.1.0 — 30 September 2026

First tagged version. Seekter had been in daily use for a month; this is the
point where that stopped being an untagged moving target.

- **12 documented job sources**, plus a file on the ones that were measured and
  found not worth the calls.
- **24 documented application form systems**, each written from real filled
  forms, plus a file of one-offs.
- A markdown tracker written only through `scripts/seekter.py`, with a normalised
  `job_key` so the same posting reached through LinkedIn, an aggregator and the
  company's own site is still caught as one.
- **39 tests** over the tracker CLI: `python3 -m unittest discover tests`.
- Guardrails that do not bend: no CAPTCHAs, no account creation, no passwords, no
  accepting terms of use, no messages sent as you, and no answer it cannot verify
  from your profile.

Numbered 0.1.0 rather than 1.0.0 because the structure is about to move: a Claude
Code plugin is next, which means the skills relocate and the tracker root stops
being "wherever you cloned this". 1.0.0 is the version where that has settled.
