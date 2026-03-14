# BMad True Agile for Game Dev Studio (GDS)

A BMad module for True Agile game development with deviation detection and capacity-first sprint planning. This is a drop-in replacement for the official `gds` module.

## What is True Agile for Games?

This module extends BMad Game Dev Studio with:

- **Deviation Detection**: Automatically detect drift between planning documents (GDD, Architecture) and actual game code
- **Capacity-First Sprint Planning**: Plan sprints based on realistic team capacity
- **Human-Centric Workflows**: Designed for human game developers using git history as source of truth

## Installation

### Via bmadboard

Install as a GitHub module to override the official gds module:

1. Open bmadboard
2. Add GitHub module: `hacking-robot/bmad-true-agile-gds`
3. Install with core module (this module will be used as `gds`)

### Manual Installation (with official bmad-method CLI)

```bash
# Install core module first
npx bmad-method install --modules core

# Clone and use as custom content
git clone https://github.com/hacking-robot/bmad-true-agile-gds.git
npx bmad-method install --custom-content ./bmad-true-agile-gds/src
```

## Module Code

`gds` - Uses the same module code as the official BMad Game Dev Studio module.

## Supported Platforms

- Unity
- Unreal Engine
- Godot
- Custom/Other

## Features

### Deviation Detection (Step 4 in Sprint Planning)

During sprint planning, automatically:
1. Scan game code for architecture drift
2. Check GDD drift (scope, features, mechanics)
3. Identify incomplete features from previous sprint

### Capacity-First Sprint Planning

1. Start with team capacity
2. Load velocity history
3. Select epics based on realistic capacity
4. Create stories that fit the sprint

### Game-Specific Workflows

- **Game Architecture**: Design game systems, patterns, and engine-specific implementations
- **Dev Story**: Develop game features with built-in testing
- **Game Test**: Comprehensive testing workflows for all platforms

## License

MIT

## Credits

Based on [BMad Game Dev Studio](https://github.com/bmad-code-org/bmad-module-game-dev-studio) by Brian (BMad) Madison
