# saisse

Skills I use with AI agents every day. Each one packs a piece of my taste or workflow into a `SKILL.md` file the agent loads and follows. They work in Claude Code, Codex and any other agent that reads skills.

Open source, MIT. Copy them, fork them, change them to fit your taste.

## Install

With the [skills CLI](https://github.com/vercel-labs/skills). It asks which agents to install to:

```bash
npx skills add victorsaisse/saisse -g
```

Everything for Claude Code and Codex, no prompts:

```bash
npx skills add victorsaisse/saisse -g -a claude-code -a codex -s '*' -y
```

One skill only: add `--skill saisse-no-fluff` (or any name from the table below).

Manual:

```bash
git clone https://github.com/victorsaisse/saisse.git
mkdir -p ~/.claude/skills ~/.agents/skills
cp -R saisse/skills/* ~/.claude/skills/   # Claude Code
cp -R saisse/skills/* ~/.agents/skills/   # Codex
```

Restart your agent after installing. Skills load on their own when a task matches. To call one directly, type `/saisse-good-taste` in Claude Code or `$saisse-good-taste` in Codex.

Update later with `npx skills update -g`.

## Skills

| Skill | What it does |
|---|---|
| [saisse-good-taste](skills/saisse-good-taste/SKILL.md) | My design taste and review process for any product surface: web, mobile, Shopify apps, dashboards, landing pages. Copy the platform's native patterns, cut text to icons, titles and buttons, make the first screen the hero, polish states and motion, treat performance as design, then review the result live like a picky senior designer. |
| [saisse-no-fluff](skills/saisse-no-fluff/SKILL.md) | How I want agents to talk to me. Replies lead with the next action, number the steps, say where you are in the task and give real time estimates. Filler gets cut, and so do AI writing tells like em dashes and hype words. |

More coming.

## Keep saisse-no-fluff always on

`saisse-no-fluff` changes how the agent writes, so it works best in every session. Add it to your global instructions once.

Claude Code:

```bash
printf '\n@~/.claude/skills/saisse-no-fluff/SKILL.md\n' >> ~/.claude/CLAUDE.md
```

Codex:

```bash
printf '\nRead ~/.agents/skills/saisse-no-fluff/SKILL.md before your first reply and follow it for the whole session.\n' >> ~/.codex/AGENTS.md
```

To pause it for one session, say "stop no fluff" or "normal mode". To turn it off for good, delete that line.

## Layout

```
skills/
  <skill-name>/
    SKILL.md        # instructions the agent reads
    references/     # extra files the skill loads when needed
```

## Credits

`saisse-no-fluff` merges and adapts three MIT skills:

- [i-have-adhd](https://github.com/ayghri/i-have-adhd) by Ayoub Ghriss: the shape rules
- [caveman](https://github.com/mattpocock/skills) from Matt Pocock's skills: the compression rules
- [humanizer](https://github.com/blader/humanizer) by Siqi Chen: the voice rules and `references/ai-tells.md`, based on Wikipedia's [Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing)

Their copyright notices are in [skills/saisse-no-fluff/LICENSE.txt](skills/saisse-no-fluff/LICENSE.txt).

## License

MIT
