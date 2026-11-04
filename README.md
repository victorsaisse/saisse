# saisse

Skills I use with Claude every day. Each one packs a piece of my taste or workflow into a file Claude can load and follow.

Open source, MIT. Copy them, fork them, change them to fit your taste.

## Install

All skills, with the [skills CLI](https://github.com/vercel-labs/skills) (Claude Code, Codex, Cursor and others):

```bash
npx skills add victorsaisse/saisse
```

One skill:

```bash
npx skills add victorsaisse/saisse --skill saisse-good-taste
```

Manual (Claude Code):

```bash
git clone https://github.com/victorsaisse/saisse.git
cp -R saisse/skills/* ~/.claude/skills/
```

Restart Claude Code after installing. Skills load on their own when a task matches, or call one by name, like `/saisse-good-taste`.

## Skills

| Skill | What it does |
|---|---|
| [saisse-good-taste](skills/saisse-good-taste/SKILL.md) | My design taste and review process for any product surface: web, mobile, Shopify apps, dashboards, landing pages. Copy the platform's native patterns, cut text to icons, titles and buttons, make the first screen the hero, polish states and motion, treat performance as design, then review the result live like a picky senior designer. |

More coming.

## Layout

```
skills/
  <skill-name>/
    SKILL.md        # instructions Claude reads
    references/     # extra files the skill loads when needed
```

## License

MIT
