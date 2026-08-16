# jsiapo-dev — Claude Code plugin marketplace

The catalog for the Claude Code plugins I maintain. It holds no code: each entry points at the
repository that owns the plugin, so a plugin is released by tagging its own repository, never by
touching this one.

```bash
claude plugin marketplace add jsiapo-dev/plugins
claude plugin install vibeward
```

The second command takes the bare plugin name. `vibeward@jsiapo-dev` also works and is only needed
if another marketplace you have registered happens to publish a plugin by the same name.

## What is in it

| Plugin | What it does | Source |
|---|---|---|
| [`vibeward`](https://vibeward.ai) | Stops the insecure prompt before Claude runs it, and audits what already shipped. Deterministic rules, no LLM call. | [`JSiapoDEV/vibeward`](https://github.com/JSiapoDEV/vibeward), branch `plugin` |

## Adding a plugin

Append an entry to `plugins` in `.claude-plugin/marketplace.json` and push. Users pick it up with
`claude plugin marketplace update jsiapo-dev`.

Two things that are not obvious and cost a re-install if you get them wrong:

- **`name` is immutable.** Once anyone has installed a plugin, its name is the key their
  `settings.json` records it under (`vibeward@jsiapo-dev`). Renaming it orphans their install.
- **Do not set `version` here.** Claude Code always reads the `version` in the plugin's own
  `plugin.json`, so a copy in this file can only ever be the stale one. The validator warns when
  they disagree.

The `source` can be a path relative to this repository or, as above, another repository:

```json
{
  "source": { "source": "github", "repo": "JSiapoDEV/<repo>", "ref": "<branch-or-tag>" }
}
```

Pointing at a **branch** means users get whatever is on it, so a release is a push to that branch.
Pointing at a **tag** pins them until this file changes.

## Before pushing a change here

```bash
claude plugin validate . --strict
```

That is the same check the Claude plugin directory review pipeline runs. `--strict` turns its
warnings into failures, which is what you want for a file whose mistakes are only visible to
someone else's install.

## Why the catalog is its own repository

It started inside the vibeward repository, which worked while there was one plugin and read wrong
as soon as there were two: nobody should have to add *the vibeward repository* to install
something that is not vibeward. The marketplace **name** is what identifies a registration, not
its location — registering a marketplace whose name is already known replaces the previous
entry — so moving the file here cost existing users nothing.
