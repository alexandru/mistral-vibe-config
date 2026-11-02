# Mistral Vibe configuration

Part of [alexandru/agents-config](https://github.com/alexandru/agents-config).
Aligned with the OpenCode harness configuration.

## Installation

Install [Mistral Vibe](https://github.com/mistralai/mistral-vibe) first:

```sh
uv tool install mistral-vibe
```

Install this configuration:

```sh
curl -fsSL https://alexandru.github.io/mistral-vibe-config/install.sh | bash
```

The installer deploys into `${VIBE_HOME:-$HOME/.vibe}`. It preserves existing
files and stops on conflicting content. To use this checkout directly:

```sh
export VIBE_HOME=/absolute/path/to/mistral-vibe-config
make -C "$VIBE_HOME"
```

Keep `VIBE_HOME` in your shell startup file when using a nonstandard directory.
Authenticate with Vibe's login flow or set `MISTRAL_API_KEY` in your environment.
Keep credentials out of this repository. The configuration does not make model
calls during installation. Node.js and Cellar prerequisites follow the same
Homebrew or apt installation conventions as the other harnesses.

Shared skills are installed globally into `~/.agents/skills`, which Vibe reads
natively, including when `VIBE_HOME` points elsewhere. They are not vendored here.

## Defined agents

Main agents:

- [Orchestrator](./agents/Orchestrator.toml) (default agent): designs and implements changes; delegates evidence, research, and execution to Junior.
- [Solo](./agents/Solo.toml): designs and implements changes; works directly.

Sub-agents:

- [Junior](./agents/Junior.toml): codebase and external evidence gathering, bounded execution, mechanical work.

```sh
vibe
vibe --agent Solo
```

Vibe separates primary profiles from sub-agent profiles. The
[Orchestrator-delegate](./agents/Orchestrator-delegate.toml) and
[Solo-delegate](./agents/Solo-delegate.toml) profiles adapt the corresponding roles
for delegation, with the same instructions. They do not introduce new roles.
Orchestrator delegates only to Junior or Orchestrator-delegate; Solo delegates
only to Solo-delegate; Junior does not delegate.

## Configuration

[config.toml](./config.toml) uses Vibe's default Mistral Medium model with high
reasoning for Orchestrator and Solo. Junior uses the same model with reasoning
disabled. Model selection remains native TOML configuration. Profile instructions
extend the built-in system prompt for primary sessions. Project instructions
continue to come from applicable `AGENTS.md` files.

The configuration enables native skills, web search, and MCP support. Add local
MCP servers with `vibe mcp add`; no machine-specific servers or credentials are
tracked. Shared skill edits are denied by the file tools. Shell commands retain
Vibe's native permission checks; file-tool denylists do not sandbox the shell.
Git authorization and role boundaries also remain explicit in the role prompts.

Vibe's unified sub-agent interface does not apply every legacy `task` permission
setting. The prompts therefore specify allowed delegation targets and prohibit
Junior delegation. Agent filters also restrict the visible profile roster.

Vibe's `/loop` and native hooks remain available. This configuration does not
install the OpenCode or Copilot reminder engines into Vibe.

## Shared skills


- [alexandru/skills](https://github.com/alexandru/skills/tree/v13.3.2)
  - `code-review`: user-invoked adapter for `code-reviewing`.
  - `code-reviewing`: review changed code for bugs, structural problems, performance issues, and unintended behavior.
  - `code-refine`: behavior-preserving refinement using the `code-refinements` lenses.
  - `code-refinements`: refinement lenses of constraints, simplicity, and parametricity.
  - `external-research`: research external documentation, public repositories, and dependency artifacts with a persistent cache.
- [mattpocock/skills](https://github.com/mattpocock/skills/tree/v1.3.1)
  - `codebase-design`: deep-module design vocabulary and principles.
  - `diagnosing-bugs`: disciplined diagnosis for hard bugs and regressions.
  - `domain-modeling`: domain language and architectural decisions.
  - `grill-me`: user-invoked adapter for `grilling`.
  - `grill-with-docs`: sharpen a plan or design while creating domain documentation.
  - `grilling`: structured decision-tree interviews.
  - `handoff`: prepare context for another agent or session.
  - `implement`: implement work from a specification or set of tickets.
  - `improve-codebase-architecture`: find and work through codebase architecture improvements.
  - `setup-matt-pocock-skills`: configure a repository for the engineering skills.
  - `tdd`: test-first development guidance.
  - `teach`: teach the user a topic through a stateful learning workspace with lessons and reference documents.
  - `to-spec`: turn the current conversation into a published specification.
  - `to-tickets`: break a plan or specification into tracer-bullet tickets.
- [VirtusLab/cellar](https://github.com/VirtusLab/cellar/)
  - `cellar`: query the APIs of JVM dependencies (Scala, Java).
- [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman)
  - `caveman`: token-efficient response modes with preserved technical accuracy.

## Updating shared skills

```sh
make update-skills
```

This reinstalls the configured global skill roster from its upstream sources.
