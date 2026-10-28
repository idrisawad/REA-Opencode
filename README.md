# REA-Opencode

**[REA](https://github.com/morluto/rea) ("Reverse Engineer Anything") as an OpenCode skill.**

This repository packages the official REA Claude Code skill —
`reverse-engineer-anything` — for [OpenCode](https://opencode.ai): the
investigation instructions, all seven reference guides, an OpenCode-specific
host section, and a ready-to-use HTTP skill catalog so it can be installed
from GitHub with one line of configuration.

The skill tells the agent *how* to investigate. The REA MCP server supplies
the *tools* (227 of them): decompilation, pseudocode, archive and package
inventories, JavaScript/Electron tracing, .NET/PE/ELF/Go/Rust inspection,
Android, captures, and more.

## What you get

| Path | Purpose |
| --- | --- |
| `reverse-engineer-anything/SKILL.md` | The skill itself (OpenCode frontmatter + OpenCode host notes) |
| `reverse-engineer-anything/references/` | Seven guides: native, JavaScript, Android, runtime, evidence, connection/recovery, environments |
| `index.json` | OpenCode HTTP skill catalog — install straight from GitHub |
| `examples/opencode.jsonc` | MCP + skill registration example |

## Requirements

- **OpenCode** V2 (skills are discovered automatically).
- **Node.js** — a version accepted by your REA release (currently 22.x, 24.x,
  or 26.x; check `node <rea>/scripts/rea.mjs doctor`).
- **REA** — the MCP server. Either a local clone
  ([morluto/rea](https://github.com/morluto/rea)) or the published package
  (`rea-agents` on npm).
- **Optional**, for native decompilation: Ghidra, Hopper, or IDA. Static
  inspection of JavaScript, .NET, PE/ELF/Go/Rust metadata, archives and APK
  resources needs no engine at all.

## Install

### 1. Clone this repository

```powershell
git clone https://github.com/idrisawad/REA-Opencode.git /.config/opencode/skills/
```

### 2. Register the skill and the MCP server

Add to `~/.config/opencode/opencode.jsonc`:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  // Skill source: pick ONE of the two forms below.
  "skills": ["D:/Tools/REA-Opencode"], // local path
  // "skills": ["https://raw.githubusercontent.com/OWNER/REPO/main/"], // or the HTTP catalog

  "mcp": {
    "servers": {
      "rea": {
        "type": "local",
        "command": ["node", "D:/Tools/rea/scripts/rea.mjs", "mcp"]
      }
    }
  }
}
```

OpenCode reloads configuration on save — no restart needed (`/reload` forces it).

**Alternative (no config entry):** copy the skill folder into a discovered
skills directory, for example:

```powershell
Copy-Item -Recurse D:\Tools\REA-Opencode\reverse-engineer-anything `
  C:\Users\<you>\.agents\skills\reverse-engineer-anything
```

OpenCode treats `~/.agents/skills` as a global compatibility source, and that
is also the location `rea setup` / `rea doctor` maintain.

## Use

Ask an investigation question, or reference the skill explicitly:

```text
@reverse-engineer-anything How does the search feature work in this app? Show the evidence.
```

```text
Reverse engineer C:\path\to\app.exe — explain how the license check behaves.
```

The skill routes the target to the right REA tool, works summary-first, and
keeps every conclusion split into observations, inferences, and unknowns with
Evidence IDs attached.

## Verify

1. The skill is listed by OpenCode and loads with `@reverse-engineer-anything`.
2. REA tools appear in the session (a healthy server advertises ~227 tools).
3. If tools are missing:

```powershell
node D:\Tools\rea\scripts\rea.mjs doctor        # registration + engine diagnostics
node D:\Tools\rea\scripts\rea.mjs --version     # CLI sanity check
```

Known Windows quirk: `rea mcp add`'s package-runner launcher can fail with
`spawn npx ENOENT`. Register the server manually in `opencode.jsonc` (as
above) or use `rea setup --client opencode --dry-run --json` to review the
plan before applying it.

## Repository layout

```text
REA-Opencode/
├── README.md
├── CONTRIBUTORS.md
├── LICENSE
├── index.json                       # HTTP skill catalog for OpenCode
├── examples/
│   └── opencode.jsonc               # MCP + skill registration example
└── reverse-engineer-anything/
    ├── SKILL.md
    └── references/
        ├── android-applications.md
        ├── connection-and-recovery.md
        ├── environments.md
        ├── evidence-workflows.md
        ├── javascript-applications.md
        ├── native-and-artifacts.md
        └── runtime-observation.md
```

## Contributors

- [@idrisawad](https://github.com/idrisawad)
- [@creative-tech-solutions-sro](https://github.com/creative-tech-solutions-sro)

See [CONTRIBUTORS.md](CONTRIBUTORS.md).

## License & attribution

This repository is licensed under the [MIT License](LICENSE).

The `reverse-engineer-anything` skill and its references derive from
**[morluto/rea](https://github.com/morluto/rea)** (MIT, © 2026 morluto),
adapted here for OpenCode. REA itself is a trademark/project of its authors;
this is an unofficial packaging of the skill, not a REA release. Get REA from
the official sources: [morluto/rea](https://github.com/morluto/rea) or
[rea.tools](https://rea.tools/).

Use REA only on software you own or are authorized to analyze.
