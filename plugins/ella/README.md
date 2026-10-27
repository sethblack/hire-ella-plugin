# Ella for Claude

Hire Ella's Claude plugin: Ella, an AI assistant who phones and emails businesses for you, from Claude.

- `.mcp.json` connects Claude to Ella at `https://www.sethserver.com/hire-ella/mcp`. The first time, Claude opens
  a sign-in: you sign in to your Hire Ella account with the emailed link and press Allow. No key is stored in
  the plugin.
- `skills/ella/` teaches Claude how to hand Ella errands: read the plan back before anyone is contacted, keep
  the businesses-or-individuals choice yours, and preview a decision before it's applied.

## Install

In Claude Code, from the folder you unzipped
[ella-plugin.zip](https://www.sethserver.com/hire-ella/claude/ella-plugin.zip) into:

```
/plugin marketplace add ./hire-ella
/plugin install ella@hire-ella
```

then `/mcp` to sign in. (Where the setup page names a GitHub marketplace, `/plugin marketplace add` it instead,
so `/plugin marketplace update` brings new versions.) Setup for claude.ai, ChatGPT and the REST API:
https://www.sethserver.com/hire-ella/mcp-setup

You need a Hire Ella account: https://www.sethserver.com/hire-ella/signup

## What to ask

- "Have Ella get three quotes to repair hail damage on my roof from these roofers: ..."
- "How is the roof errand going? Who has quoted so far?"
- "Eddie's asked whether we want the gutters done too. Tell him no."
- "Which quote would Ella pick? Go with that one."

Ella reads each errand back before anyone is contacted, and previews a decision before it's applied.

## Help and privacy

Questions or problems: sblack@sethserver.com. What Ella keeps, about you and about the people she contacts for
you: https://www.sethserver.com/privacy.html. The Hire Ella terms: https://www.sethserver.com/hire-ella/terms
