# opencode-personal

Personal opencode agents, skills, commands, and configuration.

## Structure

```
.opencode/
  agents/     - custom agent definitions
  skills/     - custom skill definitions
  commands/   - custom command definitions
  plugins/    - custom plugins
```

## Usage

1. Clone this repo locally.
2. In your project's `opencode.json`, add skills/agents/commands paths pointing here, or symlink.
3. Run `opencode` to load the configuration.
