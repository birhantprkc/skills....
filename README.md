<a href="https://interfaces.dev/">
  <img width="320" height="168" alt="interfaces.dev" src="https://ho1jr3x2dcwdu3t5.public.blob.vercel-storage.com/interfaces-og-image.png" />
</a>

[![skills.sh](https://skills.sh/b/jakubkrehel/skills)](https://skills.sh/jakubkrehel/skills)

A collection of agent skills that help you build a great interface. They cover UI, typography, colors, accessibility, layout, product writing and more.

## Skills

- [**better-interface**](skills/better-interface/SKILL.md): Combines all of the `better-*` skills into a single review across **accessibility**, **layout**, **writing**, **typography**, **color** and **UI polish**.
- [**better-ui**](skills/better-ui/SKILL.md): Polishes and improves the UI in your project. Covers **concentric border radius**, **optical alignment**, **surface depth**, **contextual icons**, **hit areas** and more.
- [**better-typography**](skills/better-typography/SKILL.md): Focuses on **type scale**, **spacing**, **sizing**, **variable fonts**, **OpenType features**, **wrapping**, **truncation** and other details that make typography feel great across your product.
- [**better-colors**](skills/better-colors/SKILL.md): Helps you build a color system and answer anything about color in your project. You can **generate palettes**, **use semantic tokens**, **convert between formats**, **check contrast** and more.
- [**better-accessibility**](skills/better-accessibility/SKILL.md): Helps your project comply with accessibility standards and best practices.
- [**better-layout**](skills/better-layout/SKILL.md): Helps with **grouping**, **alignment**, **reading order**, **progressive disclosure** and other details that make a good layout.
- [**better-writing**](skills/better-writing/SKILL.md): Focuses on improving product copy in your project.
- [**interface-review**](skills/interface-review/SKILL.md): Reviews your work across multiple categories like **UI**, **typography**, **layout**, **color**, **writing** and **accessibility** and gives you a detailed analysis of the findings. User-invoked.
- [**explain-interface**](skills/explain-interface/SKILL.md): Helps you figure out how something was built on the web. User-invoked.
- [**break**](skills/break/SKILL.md): Renders a component you choose in **every state and scenario** on a temporary page and stress tests it. User-invoked.
- [**variant**](skills/variant/SKILL.md): Builds multiple **variants** of a component you're working on and helps you iterate and pick one. User-invoked.

## Install

Both methods install the same skills. They differ in what you type to run one, so pick a method and use its names.

### CLI

Works in Claude Code, Codex, Opencode and other agents. You can choose which skills to install or install all of them.

```bash
npx skills add jakubkrehel/skills
```

Skills installed this way keep their plain names, so the change review runs as `/interface-review`.

### Claude Code plugin

Installs every skill in this repository together and updates in place. Run these inside Claude Code:

```text
/plugin marketplace add jakubkrehel/skills
/plugin install interfaces@interfaces
```

Plugin skills are namespaced under the plugin, so the change review runs as `/interfaces:interface-review` and variants as `/interfaces:variant`.

To update later, run `/plugin update interfaces@interfaces` and restart.
