---
name: ua_it_events
description: Finds new upcoming IT events in Ukraine based on user-selected interests, avoids duplicates, and helps return official registration or payment links on request.
---

# Ukraine IT Events

## Purpose

This skill is used to find upcoming IT events in Ukraine and send only new events that were not sent before.

Supported event types include:

- conferences
- meetups
- workshops
- summits
- forums
- tech talks
- community events
- bootcamps
- hackathons
- webinars
- online events

This skill must work for both:
- regular digests
- one-time searches
- follow-up requests about a specific event

---

## Main goal

Find only future IT events in Ukraine that match the user's selected interests.

Core rules:

- location: Ukraine only
- event type: any relevant IT event
- topics: defined by the user in the current request
- deduplication: do not resend events that were already sent before

---

## User-defined interests

The user may specify the directions they are interested in.

Examples:

- JavaScript
- TypeScript
- Frontend
- Backend
- Fullstack
- Web development
- AI
- Artificial Intelligence
- DevOps
- QA
- Cybersecurity
- Data Science
- Machine Learning
- Mobile
- iOS
- Android
- Cloud
- UI/UX
- Product
- GameDev
- Blockchain
- PHP
- Laravel
- WordPress
- Vue
- React
- Node.js
- Python
- Java
- .NET
- Rust
- Go
- general software engineering

The skill must use the interests provided by the user in the current request.

If the user gives multiple interests, include events matching at least one of them.

If the user does not specify interests, the skill must ask or infer that interests are missing and should not silently guess a narrow topic.

---

## Interest matching rules

Include an event if:

- its title, description, tags, agenda, or organizer page clearly show relevance to at least one of the user’s selected interests
- the event is clearly IT-related

Exclude an event if:

- it is mainly non-IT
- it is mainly business, HR, recruiting, marketing, or sales and has no clear technical relevance
- topic relevance to the requested interests is weak or unclear

If relevance is doubtful, exclude it.

---

## Geographic filter

Include only events that match one of these:

- held offline in Ukraine
- held online by Ukrainian organizers
- clearly targeted at the Ukrainian audience
- clearly presented as a Ukrainian IT event

Exclude events outside Ukraine even if the topic matches.

If the country or audience is unclear, prefer excluding the event.

---

## Time filter

Only include future events.

Do not include past events.

Do not include events that already ended.

If the event date is unclear or missing, exclude it.

---

## Preferred sources

Prefer sources in this order:

1. official event website
2. official registration page
3. official organizer page
4. reliable event platform page such as Meetup, Eventbrite, Dou, Luma, or similar trusted platform

Do not rely on low-quality reposts if an official page exists.

When possible, extract both:

- official event page
- registration or payment page

---

## State file

Use this file for deduplication:

`memory/ua-it-events-sent.json`

If the file does not exist, create it with this content:

```json
{
  "sent": []
}
```
Stored format

Each sent event should be stored in memory/ua-it-events-sent.json like this:

```json
{
  "id": "normalized_name|date|normalized_location",
  "type": "conference",
  "name": "Conference name",
  "date": "2026-05-18",
  "location": "Kyiv, Ukraine",
  "topics": ["JavaScript", "Frontend"],
  "matchedInterests": ["JavaScript", "Frontend"],
  "eventUrl": "https://example.com/event",
  "registrationUrl": "https://example.com/register",
  "sentAt": "2026-04-06"
}
```

Allowed values for type include:

conference
meetup
workshop
summit
forum
tech talk
webinar
hackathon
bootcamp
other

If registrationUrl is not available, store an empty string or reuse the official event page only when necessary.

Deduplication rules

An event is considered the same if these values match in meaning:

normalized name
date
normalized location

Use this id format:

normalized_name|date|normalized_location

Rules:

ignore minor punctuation differences
ignore case differences
ignore small URL differences
if name, date, and location are effectively the same, treat it as the same event
never resend an event whose id already exists in the state file
Search behavior

When searching for events:

read the user’s requested interests
search for future IT events in Ukraine
filter events by those interests
remove all events already present in memory/ua-it-events-sent.json
prepare a compact result with only new matching events
after producing the result, append those new events to the state file

If no interests were provided, ask the user to specify which IT directions they want.

If no new matching events are found, return:

За вказаними напрямами нових IT-подій по Україні не знайшов.

Output format

Start with:

Нові IT-події по Україні:

Then for each event include:

type
name
date
city and country, or online
short topic summary
official event link

If a registration page exists, also include:

registration link

Keep the message compact and easy to scan.

Example structure:

Meetup - JS Kyiv Community
Дата: 2026-05-18
Локація: Kyiv, Ukraine
Теми: JavaScript, Frontend
Сайт: ...
Реєстрація: ...
Workshop - Python AI Lab
Дата: 2026-06-02
Локація: Online / Ukraine
Теми: Python, AI
Сайт: ...
Реєстрація: ...
Follow-up behavior for payment or registration link

If the user asks something like:

надішли посилання на оплату
дай посилання на квиток
де оплатити цю подію
надішли реєстрацію на цю подію
дай посилання на участь

Then:

identify the requested event by name
first check memory/ua-it-events-sent.json
if needed, open the official event page
find the most direct official registration or payment link
return that link

Priority:

direct ticket or payment page
direct registration page
official event page if direct payment page is unavailable

If checkout requires login, selecting ticket type, or several manual steps, explain this briefly and return the nearest official registration page.

Never invent a payment link.

Never return unofficial or suspicious payment links.

Behavior when user refers to "this event"

If the user says:

ця подія
ця конференція
цей meetup
цей івент
це
по ній
надішли оплату на неї

Use the most recently discussed relevant event from the current conversation context.

If multiple events were discussed and the reference is ambiguous, prefer the most recent clearly mentioned event.

Data quality rules

Only include an event if all of the following are clear enough:

type
name
future date
location or clear Ukrainian origin / audience
topic relevance to the user’s selected interests
official or reliable source

If one of these is unclear, exclude the event.

Never guess missing important details.

Safety and trust rules
prefer official pages
do not fabricate dates, cities, organizers, or links
do not fabricate payment pages
if direct payment is unavailable, clearly say so
if the event is sold out, say that if confirmed
if registration is closed, say that if confirmed
Response language

Always respond in Ukrainian unless the user explicitly requests another language.

Summary rule

For event searches:

be concise
include only new events
avoid duplicates
prefer quality over quantity

For registration or payment follow-ups:

return the most direct official link available
keep the answer short and practical

When the user asks to install or set up the weekly IT events digest,
run:

`"$SKILL_DIR/scripts/uaeventscron.sh"`

If the user provides interests, pass them as arguments.
