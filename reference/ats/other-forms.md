# Other and one-off forms

- **HR-ON (same-origin iframe, e.g. EIVA):** if the uploader doesn't register the file ("no files has been uploaded"), choose "I want to type my résumé" and paste text: `pdftotext -layout <CV_PATH> -`. Quill editor: `editor.innerText = cv` + `input` event, then click → `End` → space → `BackSpace`.
- **Alfa Jobs (welovealfa.com):** scroll locks after the country combobox. Drive everything by JS: combobox `.click()`, option from `[role=option]` by text `.click()`, text via setter. Turnstile self-solves.
- **Huzzle:** `form_input`+ref one field at a time; a bulk setter script got blocked ("[Real-World Transactions]"). Draft: "Save this application?" → Save.
- **Siemens (jobs.siemens.com):** session drops silently (`/Error`, `/Login`); verify session after each step; check posting status before retrying.
- **Viterbit:** setter works. City dropdown: click dropdown → click its search box separately (first typing swallowed) → type only the part of the name with no non-ASCII letters → click option. Reject cookies: `s-rall-bn`.
- **select2-style widgets (e.g. In4Matic):** `option.selected=true`+`change` leaves the placeholder visible = not selected → real clicks.
- **BambooHR (content):** read "Minimum Experience" (e.g. Manager/Supervisor) at the end of `get_page_text` — the employer's own seniority tag.

## JobDiva (staffing-agency portals, `*.jobdiva.com/portal`)

- "Apply Now" offers three paths; **Quick Apply (No Account)** needs no account: first and last name, email, phone with its own country picker, CV, an SMS-consent box (leave unticked) and a data-storage consent for job placement (application consent). Success reads "You've applied! Good luck!". Measured 2 Oct.
- The LinkedIn apply URL carries a session in its query string; if it is cut at `?` the portal answers "Session Expired". Navigate with the full URL.

## Aplitrak / Broadbean (`aplitrak.com/?adid=…`, recruiter postings)

- Name, email, phone, CV and an eligibility radio ("currently eligible to work … in the country to which I am applying"). A JS `.click()` on `submit_btn` does nothing; a coordinate click on "Apply Now" submits and lands on `generic/submit.cgi` with "Thank you for submitting your application". Measured 2 Oct.

## Getro VC job boards (`*.getro.com/companies/<co>/jobs/<id>`)

- "Apply now" opens a modal that requires a LinkedIn URL before it reveals the employer's link, and submitting it makes the profile visible to the fund's portfolio companies. That is a talent-network opt-in the candidate has not given: hand off, or find the employer's own ATS. Measured 2 Oct.

## Microsoft (`apply.careers.microsoft.com`)

- "Apply now" goes to a sign-in page (Microsoft, LinkedIn, Google or Facebook account) before any form. Account wall: hand off. Measured 2 Oct.

## CleverStaff (`cleverstaff.net/i/vacancy-<id>`)

- Apply opens a modal: first name, surname, email, phone, one custom "CV or LinkedIn" field (a textarea plus up to two files), and a required consent to processing "for this and other vacancies". `form_input` fills the text fields.
- ⛔ **The file upload succeeds on the server and is still rejected by the form.** Measured 2 Oct: `file_upload` on the hidden `input[type=file]` posted to `/hr/public/addCvFile` (200, status ok), no file chip appeared, and Apply returned "You should fill all obligatory fields" with "Select file" under the field. The input carries `ng-click="selectFile(custom, $event)"`, which records which field the next upload belongs to; a programmatic upload skips that click, so the callback has no field to attach the file to. Call it first, then upload:
  ```js
  const f=document.querySelector('input[type=file]');const s=angular.element(f).scope();
  const c=s.$parent.custom;const root=s.$parent.$parent.$parent;root.selectFile(c,{target:f});root.$apply();
  ```
  Then `file_upload` on the same ref. An "Added file" toast and a chip under the textarea confirm it; `custom.files` holds the attachment id.
- Confirmation is a modal: "Thanks! The employer has got your application".

## Oracle Cloud HCM candidate sites (`*.fa.*.oraclecloud.com/hcmUI/CandidateExperience/…/apply/email`)

- The first screen asks for an email and says the candidate profile "will be created and kept up to date automatically". That is account creation: hand off. Measured 2 Oct (Marriott).

## Google Forms (`forms.gle/…`, `docs.google.com/forms/…`)

- A form with a file upload records the signed-in Google account's email ("The name, email address and photo associated with your Google Account will be recorded when you upload files"). If the browser's Google account is not the profile's application email, that address goes to the employer with the CV. Check the account line at the top of the form before filling; if it is the wrong address, hand off. Measured 2 Oct.
