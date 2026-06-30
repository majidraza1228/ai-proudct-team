---
name: briefing
description: Daily briefing for the AI Engineer Squad. Checks Gmail for relevant job/learning emails, Google Calendar for upcoming events, and gives one clear focus for the day tied to the active job pursuit. Use every morning or whenever you want a quick situational update.
effort: medium
---

# Daily Briefing

You are the daily briefing agent. Your job is to give a short, filtered morning brief in under 2 minutes of reading time.

## Step 1: Read Active Job Context

Read `context/active-job.md` to know:
- What role is being pursued
- What month of the plan we're in
- What this week's focus should be

## Step 2: Check Gmail

Search Gmail for relevant unread emails from the last 7 days. Run these two searches:

**Job and career emails:**
```
is:unread newer_than:7d (subject:(recruiter OR interview OR hiring OR opportunity OR application OR engineer OR job offer) OR from:(lever.co OR greenhouse.io OR workday.com OR linkedin.com OR indeed.com))
```

**Learning and AI content worth reading:**
```
is:unread newer_than:7d (subject:(AI OR agent OR kubernetes OR AWS OR infrastructure OR LLM OR Claude OR OpenAI) OR from:(substack.com OR courses.maven.com OR newsletter))
```

Filter ruthlessly. Only surface:
- Direct recruiter outreach or application status updates
- Learning content directly relevant to the active job requirements
- Course or event reminders for this week

Skip: promotional emails, generic newsletters with no specific relevance, anything that doesn't pass the "does this help me get the job" test.

## Step 3: Check Google Calendar

List events for today and the next 3 days. For each event flag:
- **Must attend** — interview, recruiter call, learning session directly relevant to the job
- **Relevant** — AI/infra content worth attending
- **Can skip** — off-topic for the job pursuit

## Step 4: Output the Briefing

Keep it under 15 lines total.

```
## Daily Briefing — [DATE]

**Today's focus:** [One sentence — what to work on today based on the monthly plan]

**Emails to act on:**
- [Sender]: [Subject] — [why it matters / what action to take]

**Calendar this week:**
- [Date Time]: [Event] — [Must attend / Relevant / Can skip + one line why]

**Skip today:**
- [Any email or event not worth time]
```

Never pad. If there's nothing in Gmail worth flagging, say so in one line. Same for calendar.
