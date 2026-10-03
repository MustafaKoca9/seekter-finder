# Candidate profile — {{FULL_NAME}}

This file is the only place personal values live. The Seekter skills read it at the start of every run and never hardcode any of it. When a preference changes, change it here, not in the skills.

Written by `/seekter-init`. Every value is concrete; the engine never fills a gap by guessing. Anything marked `ASK` is treated as unknown: the run asks the user instead of answering.

Last updated: {{DATE}}.

Disclaimer accepted: {{DISCLAIMER_ACCEPTED_DATE}} (`DISCLAIMER.md`, asked at the start of `/seekter-init`).

---

## 1. Identity and contact

| Field | Value |
|---|---|
| Full name | {{FULL_NAME}} |
| First / last split | First **{{FIRST_NAME}}**, Last **{{LAST_NAME}}**. CV parsers often get this wrong; fix it. |
| ASCII fallback | {{ASCII_NAME}} (for forms that reject non-ASCII characters) |
| **Application email** | **{{EMAIL}} — the only valid address for forms.** |
| Phone | {{PHONE_E164}} (E.164) · {{PHONE_LOCAL}} (local, when the country code is a separate field) |
| Address | {{CITY}}, {{COUNTRY}} · postcode {{POSTCODE}} · street: {{STREET_OR_ASK}} |
| Timezone | {{TIMEZONE}} |
| Nationality / residence | {{NATIONALITY}} · resides in {{COUNTRY}} |
| Languages | {{LANGUAGES_WITH_LEVELS}} |

Links:

| Link | Category when a form asks |
|---|---|
| {{PORTFOLIO_URL}} | Portfolio |
| {{LINKEDIN_URL}} | LinkedIn |
| {{GITHUB_URL}} | GitHub / Professional |
| {{OTHER_LINKS}} | |

Case-study URLs (for "link to relevant work"): {{CASE_STUDY_URLS}}

---

## 2. Documents

Files live in `profile/documents/` (not committed).

| File | Use |
|---|---|
| `profile/documents/{{CV_DEFAULT}}` | Default CV |
| `profile/documents/{{CV_ALT}}` | CV for {{ALT_ROLE_TYPE}} roles (optional) |
| `profile/documents/{{PORTFOLIO_PDF}}` | When a form demands a portfolio **file** (optional) |

---

## 3. Standard answers

| Question | Answer |
|---|---|
| Current employer / title | {{CURRENT_EMPLOYER_TITLE}} |
| Years of experience | {{YEARS}} |
| Tool years | {{TOOL_YEARS}} |
| Availability / notice / start | {{NOTICE}} (free text: "{{NOTICE_TEXT}}"; date fields: today + {{NOTICE_DAYS}} days) |
| Contract types | {{CONTRACT_TYPES}} |
| VAT / tax registration | {{VAT}} |
| Right to work in country of residence | Yes |
| Right to work elsewhere | {{WORK_RIGHTS_ELSEWHERE}} — never tick Yes for a region not listed here |
| Visa sponsorship | Remote roles: {{SPONSOR_REMOTE}}. Relocation roles: {{SPONSOR_RELOCATION}} |
| Willing to relocate | {{RELOCATE}} (see §6) |
| Current / previous salary | {{CURRENT_SALARY_OR_PREFER_NOT}} |
| Non-compete | {{NON_COMPETE}} |
| Criminal record · dismissal · disciplinary · bankruptcy | {{BACKGROUND}} |
| Outside business interests | {{OUTSIDE_INTERESTS}} |
| References / background check | {{REFERENCES}} |
| Birth date | {{BIRTH_DATE_OR_ASK}} |
| Gender / pronouns | {{GENDER_OR_PREFER_NOT}} |
| Race / ethnicity | {{ETHNICITY_OR_PREFER_NOT}} |
| Disability | {{DISABILITY_OR_PREFER_NOT}} |
| Veteran (US) | {{VETERAN}} |
| Education | {{EDUCATION}} · ATS search terms: school `{{SCHOOL_SEARCH}}`, degree `{{DEGREE_SEARCH}}` |
| How did you hear | LinkedIn / Job board. If only the company's own channels are offered with no "other" → hand off. |
| AI usage level | {{AI_USAGE}} |
| SMS / marketing / transcription consent | {{CONSENTS}} |
| Application GDPR consent | Yes. **Never** tick optional demographic-data consent. |
| Honest "No"s | {{HONEST_NOS}} |

Sponsorship / location free text (pool and preference forms):
> {{SPONSOR_PARAGRAPH}}

**Never guess (hand off or ask):** GPA · grades · "have you used our product" · community memberships · project durations · team sizes · metrics not in the fact bank · certification dates not listed here · any "tell us about a time…" anecdote not in the fact bank.

---

## 4. Salary bands

The **employer's region** decides the band, not where the candidate works from.

| Employer | Band |
|---|---|
| {{REGION_1}} | {{BAND_1}} |
| {{REGION_2}} | {{BAND_2}} |
| {{REGION_3}} | {{BAND_3}} |

- If the posting publishes a band, stay inside it.
- Annual fields: monthly × 12. Watch gross vs net.
- Number-only field without text: annual → top of band; monthly → middle.
- Free-text fallback: "Open to discussion, in line with the market range for this role."
- Clearly below band → stop and ask.
- Freelance rates: {{FREELANCE_RATES}}

---

## 5. Target roles

Apply to: {{TARGET_TITLES}}

Exclude:
- {{EXCLUDE_ROLES}}
- Any role requiring a language outside §1's list.

IC vs management line: {{MANAGEMENT_POLICY}}

Missing one core requirement is **not** a skip reason: apply and answer honestly. Four missing = noise.

Recruiter statement: *"{{RECRUITER_STATEMENT}}"*

---

## 6. Location and work model

Home country code: `{{HOME_COUNTRY_CODE}}` · Work authorization: {{WORK_AUTH}}

| Where | On-site | Hybrid | Remote |
|---|---|---|---|
| {{HOME_CITY_RULE}} | {{ONSITE}} | {{HYBRID}} | ✅ |
| {{OTHER_CITY_RULE}} | | | |

Tracks:
- **A — remote, workable from {{COUNTRY}}:** worldwide / anywhere, {{ACCEPTED_REGIONS}}, home country listed, contractor / B2B / EOR, or a timezone clause that includes {{TIMEZONE}}.
- **B — ambiguous remote:** no country stated. Answer honestly if the form asks.
- **C — relocation:** {{RELOCATION_RULE}} Priority: {{RELOCATION_PRIORITY}}.
- Never relocate to: {{FORBIDDEN_RELOCATION}}.

---

## 7. Blacklist and sensitive sectors

**Never apply, never ask:** {{NEVER_SECTORS}}

Blacklisted companies: {{BLACKLIST}}

Explicitly allowed (decisions made): {{ALLOWED_EXCEPTIONS}}

Ask first: {{ASK_FIRST_SECTORS}}

---

## 8. Fact bank

Build every free-text answer from these facts. Never reuse a sentence across companies.

Work history:

| Company | Title | Dates |
|---|---|---|
| {{COMPANY}} | {{TITLE}} | {{DATES}} |

Facts (each one concrete: a product, a number, a tool combination, a decision):
- {{FACT}}

Stories for "tell us about a time…" questions (only these may be used):
- {{STORY}}

Stack: {{STACK}}

Certifications (with dates): {{CERTIFICATIONS}}

---

## 9. Voice

Applies to cover letters, free-text answers and messages to employers.

- Punctuation rules: {{PUNCTUATION_RULES}}
- Banned phrases: {{BANNED_PHRASES}}
- Register: {{REGISTER}}
- English variant: {{ENGLISH_VARIANT}}
- Length: write what the field asks for, no more.
- Pre-submit check: {{PRESUBMIT_CHECK}}

---

## 10. Search configuration

Machine-readable queries live in `profile/search.json`. Human notes:

- LinkedIn alerts (created by the candidate, Email delivery): see `linkedin.searches` in `profile/search.json`
- LinkedIn mode: `{{LINKEDIN_MODE}}` (`email` default; `read` only with the candidate's own answer quoted here: {{LINKEDIN_READ_CONSENT}})
- Separate alerts needed for: {{SEPARATE_GEOS}} (EEA excludes the UK, Switzerland and every non-EEA European country)
- Boards and cadence: {{BOARDS}}

---

## 11. Tracker

Applications are markdown files in `applications/<YYYY-MM>/` (status in the front matter), skips are rows in each month's `skipped.md`; both written only through `scripts/seekter.py`. Statuses: pending · applied · interviewing · offer · rejected · closed · skipped. Location fit codes: A · B · C (§6).

---

## 12. Open items only the user can do

- (Seekter adds hand-offs here: CAPTCHAs, account walls, anecdotes it doesn't have, T&C acceptance.)
