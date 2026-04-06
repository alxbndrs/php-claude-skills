# Claude Skills: PHP

A collection of [Agent Skills](https://agentskills.io) for PHP application development. These skills teach AI coding agents architectural patterns, conventions, and best practices so they generate code that follows your standards.

## Skills

| Skill | Path | Description |
|---|---|---|
| [Layered Architecture](symfony/layered-architecture/SKILL.md) | `symfony/layered-architecture/` | Clean Architecture with CQRS and DDD for Symfony applications. Covers Application, Domain, and Infrastructure layer structure, module organization, and code patterns. |
| [Onion Architecture](symfony/onion-architecture/SKILL.md) | `symfony/onion-architecture/` | Onion Architecture with CQRS and DDD for Symfony. Domain at the center (entities, VOs, interfaces), Application for use-case orchestration (handlers, services), Infrastructure as outermost layer (HTTP, persistence, external integrations). |

## Usage

### Claude Code

Copy the skill directory into one of the supported locations:

**Project-level** (shared with your team via version control):

```
.claude/skills/{skill-name}/
```

**Global** (available across all your projects):

```
~/.claude/skills/{skill-name}/
```

For example, to install the Layered Architecture skill globally:

```bash
cp -r symfony/layered-architecture ~/.claude/skills/layered-architecture
```

Or at the project level:

```bash
mkdir -p .claude/skills
cp -r symfony/layered-architecture .claude/skills/layered-architecture
```

Claude Code auto-discovers skills from these paths at session startup -- no additional configuration needed.

### Other Agents

Any AI coding agent that supports the [Agent Skills specification](https://agentskills.io/specification) can use these skills. Point the agent at the `SKILL.md` file for the skill you want to use.

## Structure

Skills are organized by framework and topic:

```
{framework}/
  {skill-name}/
    SKILL.md            # Main skill definition (metadata + instructions)
    references/         # Focused code examples, loaded on demand
```

Each `SKILL.md` is self-contained with inline links to small, focused reference files. This follows the progressive disclosure model -- agents load the main instructions first and pull in specific references only when needed.

## Contributing

When adding a new skill:

1. Create a directory under the appropriate framework (e.g., `symfony/`, `laravel/`)
2. Add a `SKILL.md` with YAML frontmatter (`name`, `description`) and instructions
3. Place code examples in `references/` as small, single-topic files
4. Add the skill to the table in this README

## License

MIT
