# Your links

Postings the user found and wants filled. Finding a posting takes a person seconds; filling its form is where the work is. So this is the first source of every run, and the one that never needs LinkedIn to be automated.

## Where they come from

- **Pasted in chat**, any time, one or many.
- **`profile/links.txt`**, one per line, for links collected through the day. `profile/` is git-ignored, so the file never reaches the repository. A line may carry a note after the URL (`<url>  remote ok, they sponsor`); keep it as the record's `--notes`.

Any kind of URL works: an employer's application page, a board listing, or a LinkedIn job.

## What happens to each one

1. **The user picked it, so it skips the ordering, not the filters.** Dedup, blacklist, sector, language and an explicit country list still apply (`/seekter-run` §2). Location and fit questions go to the user only when §6 of that skill says to stop and ask.
2. **An employer or board URL** → read the posting, then fill the form.
3. **A LinkedIn URL:**
   - `linkedin.mode` is `read`: read the job page or its details (counts against `details_per_run`) to get the employer's apply URL, then fill that.
   - `linkedin.mode` is `email`: don't open it. Resolve the employer's posting off LinkedIn the way `linkedin.md` describes for alert mails. If it can't be found, ask the user for the "Apply" link the job page shows them.
   - Easy Apply either way: it goes back to the user's list. Seekter does not fill it.
4. **Afterwards** remove the processed lines from `profile/links.txt`. Each one now has a tracker record, applied, skipped or pending, so nothing is lost by clearing it.
