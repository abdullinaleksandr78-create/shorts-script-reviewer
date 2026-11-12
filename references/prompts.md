# Copy-paste prompts

Paste into any chat model (Claude, ChatGPT, Gemini) or call through an API. Use a different model from the one that wrote the script.

Replace the parts in square brackets.

## 1. Fact check

```
You are an independent fact-checker for a short educational video about [your niche] (language: [English]).
Check EVERY factual claim in the voice text. If you have search tools, use them for anything that is not textbook-basic.
A claim blocks publication if it is wrong, misleading, overstated, or gives [medical / financial / legal] advice or promises.
Answer strictly JSON:
{"verdict":"pass|fix","claims":[{"claim":"...","status":"correct|wrong|unsupported|advice","blocks":true|false,"fix":"concrete rewrite"}]}

Voice text:
[paste the spoken text]
```

## 2. Checklist review (only after the fact check passes)

```
You are an independent Shorts script reviewer (not the writer). Use this yes/no checklist, each with a one-line reason:
1 hook: does line 1 grab in the first 2 seconds (situation or surprise, not an intro)?
2 one_idea: is there exactly one clear idea, not three fighting?
3 order: hook -> mechanism -> visible result?
4 ending: does the last line pay off and close the loop with the hook?
5 spoken: short sentences that sound natural read aloud?
6 unique_hook: is the hook not a near-copy of another hook listed (same first words or same sentence)? Using a similar device (situation, number, myth) is FINE.
7 safe: no advice, diagnosis or promises?
8 panels: does each on-screen text add something (a term, number or step label) rather than copy the subtitle verbatim? Sharing one word with the voice is FINE.
Verdict "accept" only if all are yes. Vague notes are banned: each "no" needs a concrete fix.
Return strictly JSON: {"verdict":"accept|revise","items":[{"item":"hook","ok":true,"reason":"...","fix":"..."}]}

Script:
[paste the script: each line of voice + its on-screen text]

Other hooks on the channel:
- [hook 1]
- [hook 2]
```

## 3. Rewrite request (back to the writer)

```
Rewrite this Short script, fixing ONLY these notes from the checker:
[paste the failed items / blocking claims with their fixes]

Current script:
[paste the script]

[paste your original writer rules]
```

## 4. Planted-mistake test

Take an existing script, replace one line in the middle with a confident, plausible, wrong statement from your niche, and run prompt 1 on it unchanged. Do not tell the model it is a test. Caught = `"verdict":"fix"` and the planted line is in the blocking claims.
