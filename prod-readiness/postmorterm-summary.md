Here’s a summary of what each section in the template postmortem is about, including short examples:

- Title — Name of the postmortem, e.g. “Deployer API Outage Postmortem”
- Date — When the incident happened, e.g. “2016–09–05”
- Authors — List of people who wrote the postmortem (GitHub handles work fine)
- Status — Current status of the postmortem, e.g. “Complete, action items in progress”
- Summary — A one-sentence summary of the incident, usually something like “Service X was down for N minutes due to Y”
- Impact — The incident’s impact on customers and, if known, revenue or reputation, e.g. “Users were unable to do X while service Y was unavailable from 09:29 to 10:00 UTC”
- Root Causes — A list of causes that have contributed to the incident (I’ll cover root causes in part 2 of this article)
- Trigger — What triggered the outage? e.g. “Merging pull request X which started the rollout of broken software Y”
- Resolution — The action(s) that mitigated and resolved the outage, e.g. “Disabling feature X helped to mitigate the problem. Rolling back to version Y resolved it.”
- Detection — How the problem was noticed, e.g. “Pingdom detected that service X was down and paged on-call via PagerDuty”
- Actions Items — A list of actions taken (with links to GitHub issues) to mitigate or resolve the incident, and to prevent it from recurring
- Lessons Learned — What went well? What went wrong? And what was sheer luck?
