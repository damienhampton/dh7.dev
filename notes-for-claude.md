# Notes for Claude

## Hashnode API

- **Endpoint:** `https://gql-beta.hashnode.com` (POST). The old `gql.hashnode.com` now 301s to an announcement page — don't use it.
- **Auth:** `Authorization: Bearer $HASHNODE_ACCESS_TOKEN`
- **Pro plan required** for all write mutations (`createDraft`, `publishPost`, etc.) and some reads. Without Pro, you get `FORBIDDEN` — stop and tell the user, don't retry.
- **Publication ID:** `6160026db945c81a907cff1d` (dh7.dev)
- The `gql-api` skill at `.agents/skills/gql-api/` has the full schema, mutation reference, and recipes.

## File conventions

- `posts/<slug>.md` — original raw content from Damien, never overwrite
- `posts/<slug>-rewrite.md` — Claude's edited draft, ready for review

## Posting workflow

Until the publication is on a Pro plan, drafts must stay local. The intended flow once Pro is active:

1. Write/revise the post in `posts/<slug>.md`
2. `createDraft` mutation → get draft ID
3. Review on Hashnode dashboard
4. `publishDraft` mutation (or publish manually in dashboard)

## Voice guide

`tone-of-voice.md` is the source of truth. Key things to internalise:

- Identify the post shape first (personal reflective essay / analogy essay / technical how-to) — register differs significantly between them.
- Never invent a specific personal anecdote or memory. Leave a `[insert specific example here]` placeholder instead.
- British spelling throughout: realise, organise, localise, colour, whilst (only for concessive constructions).
- No padding, no engagement-bait, no selling.
- Reflective essays open with uncertainty, not a thesis. Close on an open question, not a summary.
