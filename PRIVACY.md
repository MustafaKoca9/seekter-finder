# Privacy

Seekter collects nothing. It has no server, no account and no telemetry, and the people who make it never see your data. This page says where your data does go when you use it, so you can decide what to put in your profile.

It covers this repository, the kit. The seekter.dev website is separate.

## What stays on your computer

`profile/` (your details, salary bands, CVs, the links you collect), `applications/` (the tracker, including every answer sent) and `runs/` (daily reports). They are git-ignored, so they don't reach a repository unless you change that. Deleting those folders deletes Seekter's copy of your data.

## Where it goes while Seekter works

- **Anthropic.** Seekter runs inside Claude Code, so what Claude reads while working is sent to Anthropic to be processed: your profile, CV text, job postings, form pages, and the emails it opens in your inbox. That happens under the terms and privacy policy of your Claude plan or API account ([Anthropic privacy policy](https://www.anthropic.com/legal/privacy)).
- **The employers you apply to**, and the application systems they use. Each application sends the details the form asks for, usually your name, contact details, CV and answers. From then on, that employer's privacy policy applies.
- **Job sources.** The searches it runs send your search terms to those sites. In LinkedIn `read` mode, LinkedIn sees those requests from your logged-in session, as it would see you browsing.
- **Your browser and inbox.** Seekter works in the browser you're logged in to. It uses your existing sessions; it never sees, stores or types a password. In your webmail it only reads: replies to applications, and LinkedIn job-alert emails. It never sends mail.

## What it will not do with your data

- Put it into a public repository. `/seekter-git` and the CI leak scan check every commit for your name, email, phone number and other identity values (see `SECURITY.md`).
- Use an email address other than the one your profile names for applications.
- Send it to any address or form that a web page, email or document suggests. Only you decide where it goes.
