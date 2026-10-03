# LinkedIn

## The rule, and why

LinkedIn's help page on prohibited software says it does not permit "browser plug-ins, or browser extensions that scrape, modify the appearance of, or automate activity on LinkedIn's website", and that members using them "risk having their accounts restricted or shut down" ([help article](https://www.linkedin.com/help/linkedin/answer/a1341387), [User Agreement §8.2](https://www.linkedin.com/legal/user-agreement)). Seekter drives the browser through an extension, so anything it does on LinkedIn falls inside that sentence. The risk is to the user's account, not to one application. The User Agreement also rules out the obvious workaround: one account per person, and none for someone LinkedIn has already restricted.

The useful work is filling forms, not finding postings, so Seekter treats LinkedIn as a place to **read from at most**, never to act on.

**Never, in any mode, even when asked:**
- fill or submit **Easy Apply**, or open its modal;
- any action that writes: apply, save, dismiss, follow, connect, message, react, create or edit an alert, resume or discard a draft.

**What it may do depends on `linkedin.mode` in `profile/search.json`:**

| Mode | LinkedIn pages and APIs | Where LinkedIn postings come from |
|---|---|---|
| `email` (**default**) | Never opened, never called, not even with `curl` | Job-alert emails in the inbox, plus links the user brings (`your-links.md`) |
| `read` (opt-in) | Read only, within the limits below | All of the above, plus searches, the notification feed and job details |

`read` is only ever switched on by the user, after `/seekter-init` (or a chat request) has told them in plain words that it is against LinkedIn's terms and that the risk to their account is theirs. Record their answer, quoted, in the profile. A run never switches it on by itself.

## Mode `email`: alert emails

The user's own job alerts, delivered **by email**, arrive in the inbox without anyone touching LinkedIn. Seekter reads the mail, not the site.

1. **Read the alert mails** with the method in `inbox.md`. From each card take the company, the title, the location line and the job id (the number in the `/jobs/view/<id>` href, read from the mail's HTML, never by opening the link).
2. **Dedup on the id first:** `python3 scripts/seekter.py check "https://www.linkedin.com/jobs/view/<id>/" --company "<company>"`. Writing the URL as a string is how `check` keys it (`linkedin:<id>`); it is not opened. A bare number is only normalised by `check-many`, not by `check`, and passes as new. Most records are keyed on the employer's apply URL, so the id check is the cheap first filter and step 4's check on the apply URL is the real one.
3. **Resolve the employer's own posting off LinkedIn.** Probe the public board APIs, matching the title:
   - `boards-api.greenhouse.io/v1/boards/<co>/jobs`
   - `api.ashbyhq.com/posting-api/job-board/<co>`
   - `api.lever.co/v0/postings/<co>?mode=json`
   - `POST apply.workable.com/api/v3/accounts/<co>/jobs`
   - the company's own `/careers` page, or a web search for `"<title>" "<company>"`.

   The slug is usually the company name lower-cased, with or without hyphens; try two or three spellings before giving up.
4. **Found** → an ordinary candidate from here: dedup again on the apply URL, filter (`/seekter-run` §2), fill. Put the LinkedIn id in `--notes` so a later alert for the same job dedups.
5. **Not found, or Easy Apply** → the report's **"On LinkedIn, yours to send"** list with the LinkedIn URL, and `pending` in the tracker.

**A digest mail is a sample, not the list.** Measured 4 Oct: a mail headed "30+ new jobs match your preferences" carried 6 job cards and a "See all jobs" link. The other 24 are only on LinkedIn. Narrow alerts that match a handful of postings a day lose almost nothing to this cap; broad ones lose most of their matches. In `email` mode that is the main lever, and it is the user's to pull (next section).

## Setting up the alerts (the user does this, on LinkedIn)

Seekter cannot create or edit alerts. It tells the user what to set up: one alert per row of `linkedin.searches`, delivered by **Email**, to the address the inbox step reads. What was measured about alert quality:

- **No quotes.** Measured over 7 days:

  | Query shape | Quoted | Unquoted |
  |---|---|---|
  | Two-word niche title · EEA · remote | **0** | 25 |
  | Two-word niche title · Worldwide · remote | 7 | 25 |
  | Title with a slash variant · EEA · remote | 13 | 25 |
  | Common two-word title · EEA · remote | 25 | 25 |

- **LinkedIn does not stem or merge title variants.** Each variant the candidate's field uses needs its own alert.
- **EEA excludes the UK, Switzerland and every non-EEA European country.**
- **The Remote filter hides hybrid and on-site roles**, so a relocation geography needs a second alert without it.
- **Small markets:** alert on the broadest single word of the discipline; local postings use local title conventions.
- **Narrow beats broad** because of the six-card cap above. Prefer several narrow alerts to one broad one.
- **Job preferences ("Open to work") titles are a closed taxonomy**, max 5. Pick the nearest standard title and note the substitution in the profile.

## Mode `read`: limits

Detection comes from volume and rhythm, so the limits are the point of this mode. Defaults live in `linkedin.read_limits` in `profile/search.json`:

| Limit | Default | Meaning |
|---|---|---|
| `searches_per_run` | 15 | Search requests per run, pages included. Rows beyond it rotate: start the next run where this one stopped, and keep the index in `runs/linkedin-state.json` |
| `details_per_run` | 60 | Job detail requests per run, job pages opened in the browser included. Fetch details only for title matches |
| `min_gap_seconds` | 3 | Pause between any two LinkedIn requests. Requests run one after another, never in parallel |
| notification feed | once per run | One page load, one harvest |

**Stop signals.** Any of these ends LinkedIn reading for the run at once, before another request:
- an HTTP 429 or 999, or a 403 on an endpoint that worked earlier in the run;
- a redirect to `/checkpoint/`, `/authwall`, `/uas/login` or a CAPTCHA page;
- page text about a restriction or unusual activity, in whatever language the account's interface uses ("restricted", "unusual activity", and their equivalents).

Then set `linkedin.mode` back to `email` in `profile/search.json`, say so at the top of the report, and carry on with the other sources. Switching it back to `read` is the user's call.

## Mode `read`: methods

Requests run in the page context of an open `linkedin.com` tab. `window.CSRF = document.cookie.match(/JSESSIONID="?([^";]+)"?/)[1]`. Every fetch is followed by `await new Promise(r=>setTimeout(r, gap*1000))`; at the default gap, eight requests fit inside one JS call's 45 s budget.

**Searches** (Voyager REST, relevance order):
```js
window.R=window.R||{};
window.SEARCH=async function(key,kw,geo,remote,tpr,start){
  // Relevance order. Never add sortBy:List(DD): see "sortBy" below.
  var f=[];if(remote)f.push('workplaceType:List(2)');if(tpr)f.push('timePostedRange:List('+tpr+')');
  var q='(origin:JOB_SEARCH_PAGE_OTHER_ENTRY,keywords:'+encodeURIComponent(kw).replace(/\(/g,'%28').replace(/\)/g,'%29')+
        ',locationUnion:(geoId:'+geo+'),selectedFilters:('+f.join(',')+'),spellCorrectionEnabled:true)';
  var u='/voyager/api/voyagerJobsDashJobCards?decorationId=com.linkedin.voyager.dash.deco.jobs.search.JobSearchCardsCollection-220'+
        '&count=25&q=jobSearch&query='+q+'&start='+(start||0);
  var r=await fetch(u,{headers:{'csrf-token':window.CSRF,'accept':'application/vnd.linkedin.normalized+json+2.1'}});
  window.R[key]={status:r.status,j:r.ok?await r.json():null};return r.status;};
```
- In `j.included`, records whose `entityUrn` is `...jobPosting:<id>` carry `title`: id and title in one request. Filter by title, then fetch details only for candidates.
- `count` max 25; `start=0/25/50`. `tpr`: `r86400`, `r259200`, `r604800`, `r2592000`. `workplaceType:List(2)` = remote.
- **Parentheses in a keyword silently return 0** unless escaped: `encodeURIComponent` leaves `(` and `)` alone and the query DSL is built from them. The `replace` calls above handle it.
- **Relevance, never `sortBy:List(DD)`.** Date order on a loose multi-word query ranks by posting time across everything matching any word: measured 23 Sept, relevance 25/25 on-discipline, date order 1/25. Use `timePostedRange` for freshness.
- **Don't filter on Easy Apply (`f_AL`).** It breaks keyword matching. Tell Easy Apply from `applyMethod` instead.
- **geoIds:** `91000002` EEA · `92000000` Worldwide · `102105699` TR · `102890719` NL · `101282230` DE · `104738515` IE · `105646813` ES · `103350119` IT · `105072130` PL · `105015875` FR · `100364837` PT · `101165590` UK. EEA doesn't cover the UK or Switzerland. A new one can be read out of a search URL the user sends.
- **Country geo + remote trap:** remote plus a single-country geo mostly returns global roles that accept that country, not local companies.
- If the endpoint dies, stop and report. Finding a replacement means watching the network panel while paging through a search, which is a job for a session the user is watching, not a run.

**Notification feed** (once per run): open `https://www.linkedin.com/notifications/?filter=jobs_all`, check the highlighted pill reads Jobs (`?filter=job_alerts` opens on All and gave 20 ids against 48), scroll once, then harvest without clicking anything:
```js
const ids=new Set();
document.querySelectorAll('a[href*="originToLandingJobPostings"]').forEach(a=>{
 const p=new URL(a.href,location.origin).searchParams.get('originToLandingJobPostings');
 if(p)p.split(',').forEach(x=>ids.add(decodeURIComponent(x).trim()));});
document.querySelectorAll('a[href*="/jobs/view/"]').forEach(a=>{const m=a.href.match(/\/jobs\/view\/(\d+)/);if(m)ids.add(m[1]);});
JSON.stringify([...ids])
```
The feed and the searches are different channels: on seven days between 22 Sept and 2 Oct, most notification ids appeared nowhere in that day's search results (50 of 63 on 2 Oct). The feed is cheap, one page; keep it.

**Job details:**
```js
const r=await fetch('/voyager/api/jobs/jobPostings/'+id+'?decorationId=com.linkedin.voyager.deco.jobs.web.shared.WebFullJobPosting-65',
  {headers:{'csrf-token':window.CSRF,'x-restli-protocol-version':'2.0.0'}});
const j=await r.json();const d=j.data||j;   // the body is j, not j.data
const am=d.applyMethod||{};
const url=(am['com.linkedin.voyager.jobs.OffsiteApply']||{}).companyApplyUrl||'';   // empty = Easy Apply
```
- Keep full URLs in `sessionStorage`, not `window` (lost on navigation) and not in printed output (`?`, `=` and `&` trigger `[BLOCKED: Cookie/query string data]`; print hostnames, or replace those characters).
- `closed=false` is not a liveness check; a posting can stop accepting applications within hours. The employer's page decides.
- `companyDetails` sometimes returns no name; take it from the description's first sentence.

## Easy Apply

Listed, never filled, in both modes. The report gives each one's LinkedIn URL and the CV to pick, and the user sends them.
