# IT Events Skill

Link: https://clawhub.ai/frankyjo/searchitevents

Use natural language to ask for IT events.

## One-time search

Write:

- `Show me IT events about AI and Frontend in Germany`
- `Find React meetups worldwide`
- `Покажи IT-події по Python у Польщі`

You must specify:

- topic / interests
- country or `worldwide`

If something is missing, the bot should ask уточнення.

## Weekly digest

Write:

- `Show me every week IT events about AI and Frontend in Germany`
- `Set up a weekly digest for React worldwide`
- `Показуй мені щотижня IT-події по Python у Польщі`

The bot should:

1. ask for missing topic or location if needed
2. create a weekly cron job after both are known

## Follow-up

After an event is found, you can ask:

- `Send me the registration link`
- `Give me the payment link`
- `Надішли посилання на оплату`

## Rules

- Topic is required
- Location is required: country or `worldwide`
- The skill accepts any language
- If the request is one-time, it should search once
- If the request is weekly, it should set up cron
