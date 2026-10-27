---
name: ella
description: Hand phone and email errands to Ella, the user's AI assistant from Hire Ella, and check how they went. Use when the user asks to get quotes, call or email businesses, confirm appointments, follow up with someone, take calls about a listing, answer what a contact asked, choose between quotes, or asks how an errand or call went.
---

# Ella errands

Ella is an AI assistant who phones and emails real people for the user, from the user's own Ella number, and
takes their callbacks. She works for one Hire Ella account: the one the user signed in to when they connected
her. Every tool here (the `ella` MCP server) sees only that account.

If the tools aren't there, the user hasn't connected Ella yet: point them to
https://www.sethserver.com/hire-ella/mcp-setup.

## Before anyone is contacted

Errands reach real people, and a call can't be taken back.

- **Read the plan back and get a yes** before `create_errand` or `add_errand_contacts`: the title, the brief, who
  she'll contact (names and numbers or addresses), how (calls, email, or both and in which order), the days and
  hours, and the last day.
- **Businesses or individuals is the user's call, never yours.** Set `businesses_attested` only when the user has
  told you every contact is a business reached at its business number. For people at home, the errand is
  `audience` "individuals" and each needs `consent`: where and when they agreed to be contacted by an AI
  assistant for the user. Never invent consent or a source for it; if the user doesn't have it, Ella can't call
  them.
- Write the brief as if for a temp: exactly what to get done on each call, with real dates ("Tuesday, November
  3", never "tomorrow"), and what she must not agree to. End it with a paragraph "Shareable if asked:" listing
  what she may tell people (an address, the size of the job). She shares nothing else.
- Send an `idempotency_key` on `create_errand` and `add_errand_contacts` (something like "roof-quotes-2026-11-03"),
  and reuse it if you retry, so nothing is made twice.
- Ella checks every errand before she contacts anyone. `get_errand` shows `check`: until it has passed, nobody is
  called. A held errand waits for review; a refused one won't run.

## Tools

| Do | Tool |
|---|---|
| Start an errand | `create_errand` |
| Put more contacts on one | `add_errand_contacts` |
| Take a contact off | `remove_errand_contact` (the `target_id` from `get_errand`) |
| Pause, resume or cancel | `set_errand_status` (cancel can't be undone: confirm first) |
| The user's errands | `list_errands` |
| One errand, contact by contact | `get_errand` |
| One call or email thread: summary, transcript | `get_call` (a `call_ids` entry from `get_errand`) |
| Answer what a contact asked | `answer_questions` |
| Choose between offers | `decide_errand` |
| Saved contacts, flags and consent | `list_contacts` |
| Plan, calls used, the Ella number | `usage` |

A result with an `"error"` key means it didn't happen; `problems` lists what to fix. Fix them and try again
rather than guessing around them.

## Starting an errand

```json
{
  "title": "Roof repair quotes",
  "brief": "Get a quote to repair hail damage on a 2,000 sq ft shingle roof at our shop. Ask for their earliest start and whether they're insured. Don't agree to anything or book a visit.\n\nShareable if asked: the shop is at 1200 Main St, Huntsville, TX; the roof is about 15 years old.",
  "contacts": [{"name": "Eddie", "company": "Eddie's Roofing", "phone": "936-555-1234"}],
  "businesses_attested": true,
  "time_zone": "America/Chicago",
  "window_start": "09:00",
  "window_end": "17:00",
  "end_date": "2026-11-06",
  "idempotency_key": "roof-quotes-2026-11-02"
}
```

- Calls by default. `channels` "email", "email_call" or "call_email" need email turned on for the account;
  "call_sms" (call, then text those who agree to it) needs the account's number registered for texting.
- Ella calls between 8 AM and 8 PM where the contact is; the window narrows that. At most 5 tries a contact.
- `kind` "inbound" is an errand that contacts nobody and only answers people who call the Ella number about it
  (a listing, a job posting): give no contacts, and `may_share` and `collect` for what she tells and asks them.

## Checking on it

`get_errand` returns `progress` and, for each contact, `status`, `last_summary`, `next_try`, `call_ids`, any
`offer`, and `waiting_on_you`. Summarise it in plain words: who's done, who she's still trying and when, who's
waiting on the user and what they asked, and any offers side by side. Use `get_call` when the user wants what was
said.

## Questions only the user can answer

When a contact asks something Ella can't answer (a gate code, whether Saturday works), they wait on the user.
Show the user each question from `waiting_on_you` and pass on **their own words** with `answer_questions`
(`[{question_id, answer}]`, or `skip` for ones she shouldn't answer). Ella gets back to the contact with them.
Don't answer for the user.

## Deciding between offers

Choosing is the user's decision. When they've picked:

1. Call `decide_errand` with `chosen_target_ids` (and `message` in their words) and no `confirmed`. It previews
   who hears what and returns a `plan_key`. Nothing is applied.
2. Show the user the preview. Only after they say yes, call `decide_errand` again with `confirmed` true and the
   same `plan_key`.

Ella waits 10 minutes before telling anyone; `decide_errand` with `undo` true takes it back until someone may
have heard.
