# Card projects

Every card project gets its own folder here: `projects/<project-id>/`.

Use a stable lowercase ID with hyphens, for example `nael-family` or `arbeit-office`. The display name may contain spaces, accents, and any preferred capitalization.

Each project must follow [the project structure](../docs/project-structure.md) and expose its overview at `index.html` inside its folder.

[`_default/`](_default/README.md) is the reusable starter. Copy it into a new project folder, then fill in the new identity and configuration. It is not an active card project and must never contain real project references or cards. Its [initial overview](_default/index.html) works offline; automatic project creation and overview regeneration still need implementation.

The starter's [`references/`](<_default/references/README.md>) separates original source files from structured AI analyses, extracts, entity records, and reference selections for cards and templates.

No card projects have been created or imported yet. Existing Nael and Arbeit files remain in their original locations until migration is requested.
