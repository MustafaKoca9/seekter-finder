# Responsible use

Seekter is a free, open-source kit that runs on your own computer, inside your own Claude Code session and your own browser. There is no Seekter service, account or server, so there are no terms of service to accept. What follows is what you take on by using it. It is not legal advice; if your situation depends on one of these points, ask someone qualified where you live.

## You are the applicant

Every application Seekter sends goes out in your name, from your browser, with your details. You are responsible for what it says.

- Seekter fills forms only from what you wrote in `profile/`. If something there is wrong, it will be wrong in every form. Keep it true and current.
- When a form asks something your profile can't answer, Seekter stops and asks you instead of guessing. Answer honestly; a guess becomes a statement you made.
- Every free-text answer it sends is saved in the application's file under `## Answers submitted`. Read them.
- Some forms ask you to declare something (that the information is true, that you didn't use AI, that you agree to arbitration). Seekter does not tick those for you. They are yours to decide.

## Other sites' terms are yours to follow

Job boards, LinkedIn, application systems (Greenhouse, Ashby, Lever, Workday and others) and your email provider each have their own terms. Using Seekter doesn't change them, and you are the one who agreed to them.

- **LinkedIn** does not permit browser extensions that scrape or automate its site, and it can restrict or close accounts that use them. Seekter never fills Easy Apply and never takes an action on LinkedIn. By default it doesn't open LinkedIn at all. If you switch `linkedin.mode` to `read`, it reads searches and job details within limits; that is still against LinkedIn's terms, and the risk to your account is yours. Details: `reference/sources/linkedin.md`.
- **Application systems** let each employer flag or block applications on their own side. Greenhouse, for example, gives employers a fraud-risk report and a blocklist; it says these stay within each employer and make no automatic rejections. Seekter applies from your own browser, at most once per posting, and never tries to get around a CAPTCHA or a bot check. If a source blocks automation, stop using it there.
- **Employers' own rules.** A posting may ask candidates not to use AI or automation. Whether you apply anyway is your decision.

## No guarantees

Seekter is provided "as is", under the [MIT License](LICENSE), without warranty of any kind. It can misread a posting, fill a field wrongly, miss a deadline or apply somewhere you would rather it hadn't. It does not promise interviews, offers or any result.

It is not affiliated with, endorsed by or sponsored by LinkedIn, Anthropic, or any job board, application system or employer it mentions.

## Costs

Seekter itself costs nothing, and nobody behind it charges you or takes a cut. **Running it is not free, though.** It needs Claude Code, which means a paid Claude plan or API usage on your own Anthropic account, billed to you by Anthropic under its terms. A daily run reads many pages and fills many forms, so it uses a lot of Claude: on a plan it counts against your usage limits, and on the API it is billed per use. Check what your plan covers before running it every day.
