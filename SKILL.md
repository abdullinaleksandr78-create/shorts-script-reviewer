---
name: shorts-script-reviewer
description: "Review a Shorts script yes/no; test the checker."
when_to_use: "Use when you have a short-form video script (YouTube Shorts, Reels, TikTok) and want an independent pass/revise verdict before you spend money on visuals and voice. Also use to test whether your AI reviewer actually catches errors. Triggers: review this script, check my Short, is this script ready, test my reviewer, planted mistake test."
version: 1.0.0
license: MIT
metadata:
  hermes:
    tags: [shorts, scripts, review, quality-control]
    related_skills: []
---

# Shorts Script Reviewer

The reviewer station from the "AI made 30 Shorts while I slept" factory. Two jobs:

1. **Review** a script with a fixed yes/no checklist and return a verdict: `accept` or `revise`.
2. **Check the checker**: plant known mistakes into scripts and confirm the reviewer catches them.

Everyone checks the AI's work. Almost nobody checks the checker.

## When to Use

- A script is written and you want a verdict before you make images, voice or edit.
- You changed your reviewer prompt or model and want to know if it still catches errors.
- Run it on every script, and run the planted-mistake test at least once per batch.

## Golden rules

- **The reviewer is not the writer.** Use a different model (or at least a fresh chat with no writing history) from the one that wrote the script. A model grading its own work is too kind.
- **Give the reviewer only the script, the rules and the other hooks.** Never pass the writer's explanation of why the script is good.
- **Yes/no, not vibes.** Every item gets `ok: true/false` and a one-line reason.
- **Every "no" comes with a concrete fix.** "Make it more engaging" is banned. "Replace beat 1 with the situation from beat 3: 'You swallowed gum. It's still in there?'" is a fix.
- **Facts block, style revises.** A wrong or overstated fact blocks the script even if everything else is perfect.
- **Limited rounds.** Allow 1-2 rewrite rounds. Still failing after that = reject the script and move on. Endless loops burn money and produce mush.
- **Write rules carefully.** The AI follows them more literally than you meant. If one rule kills a large share of scripts, check whether the rule is wrong, not just the scripts.

## Step 1. Fact check (run first)

Ask the reviewer to list every factual claim in the spoken text and mark each one:

- `correct` - true as spoken, without needing extra context;
- `wrong` - false;
- `unsupported` - may be true, but overstated or not backed ("all", "always", exact numbers nobody can source);
- `advice` - tells the viewer what to do about health, money or law (only if your niche must avoid that).

Any `wrong`, `unsupported` or `advice` claim gets `blocks: true` and a concrete rewrite. If the reviewer has web search or a database (PubMed, docs, official sites), tell it to use it for anything that is not textbook-basic.

Verdict: `fix` if anything blocks, otherwise `pass`. On `pass`, go to step 2.

## Step 2. The 8-question checklist

Answer each with yes/no and a one-line reason. Verdict `accept` only if all 8 are yes.

| # | item | Question |
|---|---|---|
| 1 | hook | Does the first line grab in the first 2 seconds (a situation or a surprise, not an intro like "Today we'll talk about")? |
| 2 | one_idea | Is there exactly one clear idea, not three fighting for attention? |
| 3 | order | Does it go hook -> what happens / how it works -> a visible result? |
| 4 | ending | Does the last line pay off and close the loop with the hook? |
| 5 | spoken | Short sentences that sound natural read aloud? No jargon without a plain word? |
| 6 | unique_hook | Is the hook not a near-copy of another hook on your channel (same first words or same sentence)? Reusing a device (situation, number, myth) is FINE. |
| 7 | safe | No advice, diagnosis, guarantees or promises your niche can't make? |
| 8 | panels | Does each on-screen text add something (a term, number or step label) rather than copy the subtitle word for word? Sharing one word with the voice is FINE. |

The "FINE" lines in 6 and 8 are there for a reason: without them the reviewer rejects almost everything as "similar". Adapt 7 and 8 to your niche, keep the yes/no format.

## Step 3. Output format

Return strictly JSON so a script (or you) can act on it:

```json
{
  "fact_check": {
    "verdict": "pass|fix",
    "claims": [{"claim": "...", "status": "correct|wrong|unsupported|advice", "blocks": false, "fix": ""}]
  },
  "review": {
    "verdict": "accept|revise",
    "items": [{"item": "hook", "ok": true, "reason": "...", "fix": ""}]
  },
  "final": "accept|revise|reject"
}
```

If the fact check says `fix`, skip the checklist (`review` = null) and send the script back with the fact fixes first.

`final`: `accept` = fact `pass` and all 8 yes. `revise` = send back to the writer with ONLY the failed items and their fixes. `reject` = it already used its rewrite rounds.

## Step 4. Send back to the writer

Give the writer the current script plus only the failed items with their fixes: "Rewrite this script, fixing ONLY these notes". Then review the new version from step 1 again.

## Check the checker: planted-mistake test

Your reviewer said "pass". Does that mean the script is good, or that the reviewer is asleep? Find out:

1. Take 2 scripts that already exist (good ones are best).
2. In each, replace one line in the middle with a mistake you know is wrong, and add it to the claims list. Make it sound confident and plausible, in your niche. Examples:
   - anatomy: "Most of the food we eat is absorbed into the blood directly through the stomach wall." (wrong: small intestine)
   - anatomy: "Insulin, the hormone that lowers blood sugar, is made by the liver." (wrong: pancreas)
   - money: "Index funds are guaranteed not to lose money over 10 years."
   - tech: "Python lists are immutable."
3. Run the reviewer on them exactly as on any normal script: same prompt, same model, no hint that this is a test.
4. Caught = verdict `fix` AND the planted line is among the blocking claims. Passing the script, or blocking it for some other reason only, = missed.
5. Score it: caught X of N. Anything below N of N means the reviewer is not ready. Strengthen the prompt, give it search, or use a stronger model, then test again.

Re-run the test whenever you change the reviewer's prompt or model. In my factory the checker caught 2 of 2 on both runs, so 4 of 4 total.

Ready prompt templates: [prompts.md](references/prompts.md).

## What this skill does not do

- It does not prove facts are true. It catches what the reviewer model and its tools can catch. For health, money or law content a human still owns the final call.
- It does not check images or the finished video. That needs a separate gate that looks at the actual file.
