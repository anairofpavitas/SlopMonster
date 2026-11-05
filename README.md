# SlopMonster

![The five SlopMonster mascots, one for each scoring category](docs/img/hero.png)

SlopMonster checks English writing for common AI writing patterns and provides
instructions for editing the draft. Use it on a README, a landing page, an email or
a script.

The Python checker reports matches in five categories and gives the text a score
out of 5. The editing workflow asks an AI agent to review the draft for its intended
reader, revise it, and check it again. This fork removes the second-model step, so
drafts stay with the agent you are already using and nothing goes to another model.

A score of 5/5 means the checker found no scored matches. You still need to review
whether the text makes sense, supports its claims and gives the reader what they
came for. The previous version of this README passed while using repetitive
metaphors and vague contrasts. That exposed a gap in the editing guidance.

## Quick start

The checker uses Python 3 and its standard library. It needs no API key or packages
to install.

```bash
git clone https://github.com/ItsssssJack/SlopMonster
cd SlopMonster

# Check a short draft.
python3 tools/deslop.py --text "It's not just a tool, it's a game-changing journey."
# Scores 3/5 and exits with status 1.

# Check a Markdown file.
python3 tools/deslop.py README.md

# Check text extracted from an HTML file.
python3 tools/deslop.py index.html
```

Each category with a match costs one point, regardless of how many matches it
contains. The command exits with status 0 at 5/5 and status 1 below 5/5. Empty input
also fails. Checking a file leaves its contents unchanged.

For Markdown, the checker skips fenced code blocks, inline code, images and
struck-through examples. For HTML, it removes tags and script/style contents. It
doesn't render the page, so text hidden by CSS can still be included.

## Edit a draft

Give your agent [SKILL.md](SKILL.md) and the draft. Include the intended audience
and where the writing will appear if those are unclear from the text.

1. Run the checker to identify the patterns it recognizes.
2. Review the draft's purpose and order. A README should explain the tool and show
   how to use it. An email should make its reason for writing clear.
3. Rewrite unclear or repetitive passages. Check that each sentence adds useful
   information and that claims match the supplied facts.
4. Compare the edit with the source and run the checker again.

Using another model provides another editing pass. It doesn't guarantee better
writing. Review the result for changed facts and lost context before accepting it.

### Use as an agent skill

For Claude Code, copy this folder to `~/.claude/skills/slopmonster/` and invoke
`/slopmonster`. For Codex or another agent, ask it to follow [SKILL.md](SKILL.md).

## What the checker flags

The current score has five categories:

| Category | What it checks |
|---|---|
| Vocabulary | Listed words such as `delve` and `seamless`, including selected word forms. |
| Sentence patterns | Listed constructions such as `not just X, but Y`, stacked hedges and certain self-answering questions. |
| Punctuation | Repeated em dashes or stacked hyphenated compounds within a 220-character window in a sentence, plus semicolon density. |
| Three-item lists | Two specific list patterns that often occur in promotional copy. |
| Possible unsupported proof | Numbers near nouns such as `users` or `customers`. The checker cannot verify the claim. |

The patterns are in [tools/deslop.py](tools/deslop.py). The broader editing
catalogue is in [references/signs-of-ai-writing.md](references/signs-of-ai-writing.md).
Some entries in that catalogue require editorial judgment and have no automated
check.

If a flagged customer count is accurate and you have evidence for it, use:

```bash
python3 tools/deslop.py index.html --allow-proof
```

This option still reports proof matches but removes their score penalty. It
doesn't check the evidence. In this mode, 5/5 can include proof warnings.

## Review beyond the score

Read the draft in the context where someone will encounter it. Check these points
before accepting an edit:

- **Audience and purpose:** Does it answer the reader's likely questions at the
  right level of detail? Explain technical terms where they first matter.
- **Opening:** Does the first paragraph explain the subject or establish a useful
  reason to keep reading? Avoid adding a second hook that delays the explanation.
- **Progression:** Does each sentence build on the previous one? Remove repeated
  metaphors and contrasts that add no information.
- **Claims:** Can you support each factual statement? Describe optional behavior
  with its conditions. For example, a command's failure status only stops a
  workflow if that workflow is configured to act on it.
- **Voice:** Does the wording suit the author and format? Contractions are fine
  where natural. Forced fragments and invented opinions can make the edit worse.

See [references/principles.md](references/principles.md) for more editing guidance
and examples. Never add a statistic or testimonial to make a sentence sound
specific. If information is missing, flag it for the author.

### Limits

The checker uses English patterns and has no language detection. Text in another
language can score 5/5 because those patterns don't match.

It can flag ordinary wording and miss poor writing. It cannot determine who wrote
the text, verify facts or judge whether a hook suits the audience. Use its findings
as prompts for review. A passing score alone doesn't establish writing quality.

## Optional CI check

You can run the checker in continuous integration (CI), the automated checks a
repository runs when code changes. Because a score below 5/5 returns a failure
status, a workflow that runs the command can fail on flagged copy.

This repository's [workflow](.github/workflows/slop.yml) runs the regression tests
and checks both this README and a sample file. Copy it to your repository and set
the file paths you want checked. Blocking a merge or deployment requires your own
repository rules or deployment configuration.

```bash
python3 tools/test_deslop.py
python3 tools/deslop.py README.md
```

## Examples and sources

- [Jasper homepage excerpt](examples/jasper-live-run.md): a recorded editing run
  with the commands and output from that run. Scores can change as rules change.
- [Ridgeline Roofing](examples/ridgeline-roofing.md): a longer worked example with
  before-and-after copy. Check any business facts before reusing a line.
- [Source list](references/sources.md): Wikipedia's *Signs of AI writing* and the
  open-source humanizer projects used to assemble the catalogue. The marketing
  guidance also draws on Steve Krug, Daniel Priestley and Alex Hormozi.

[MIT license](LICENSE).
