# Changelog

The version is the git tag; there is no version file. Each release is also on
[the releases page](https://github.com/selfishprimate/seekter/releases) with the
same text.

## v0.1.1 — 1 October 2026

A bug-fix release. If you are on 0.1.0 and run `/seekter-run`, one of the five
sources has been quietly doing nothing for you, and that is what this fixes.

### LinkedIn Easy Apply never ran if your LinkedIn is not in English

The Easy Apply driver matched on the English button names: "Easy Apply", "Next",
"Review", "Submit application". On a Turkish account those read "Kolay Başvuru",
"İleri", "İncele" and "Başvuruyu gönder", and every check failed.

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
