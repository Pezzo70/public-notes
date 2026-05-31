---
draft: true
---

# Codex Notes

This repository is the public Quartz content repository for the digital garden.

- Treat every Markdown file here as publishable website content.
- Follow the Quartz configuration from the parent `digital-garden` repo, especially the `content` folder contract.
- Do not assume this is a normal Obsidian vault, even though Quartz supports some Obsidian-flavored Markdown.
- Avoid Obsidian plugin-only features such as Dataview queries, Tasks plugin syntax, private vault embeds, or local-only workspace conventions.
- Keep public pages self-contained: use Markdown links that Quartz can resolve, frontmatter that Quartz understands, and assets stored inside this repository.
- Put images and attachments under `Resources/` unless an existing local pattern clearly says otherwise.
- Use `draft: true` in frontmatter for content that should not be published yet.
- Never place private notes, daily journal entries, credentials, personal task state, or unpublished private context here.

`public-notes` is intentionally a separate Git repository because it is shared by the private notes vault and mounted into Quartz as its `content` folder.
