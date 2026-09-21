# LetsDefend Blue Team Cases

My incident response practice on the [LetsDefend] Blue Team platform. Each case is a SOC alert I investigated and closed, written up in the format of an incident ticket.

**Goal:** learn and develop incident response skills through regular hands-on practice.

## Report format

Each write-up covers: Summary, Timeline, Artifacts (IOCs), Root Cause, Impact, Action Taken, Recommendations.

## Notes

- Training platform only. No real organisations or data.
- Write-ups are in my own words.
- IOCs are defanged, for example `185[.]107[.]56[.]141`.

## Summary format

- Summary (What happened): A 2–3 sentence overview of the alert and the final verdict (True Positive or False Positive).
- Timeline (When it happened): Chronological log of the event, your detection, containment, and resolution.
- Root Cause (Why it happened): The vulnerability, user action, or system gap that allowed the activity.
- Impact (Who/What was affected): The exact hostnames, usernames, IP addresses, and data involved.
- Action Taken (How it was fixed): The technical steps you took to isolate the threat and clean the system.
- Recommendations (Future prevention): Long-term fixes (e.g., blocking an IP permanently, updating an EDR rule).
