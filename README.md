# Saturday CLI - Comprehensive Development Prompt

## Project Overview

Build a sophisticated Go CLI tool called **Saturday** that generates and manages Playwright test automation frameworks with TypeScript, Cucumber, and optional ML-powered visual validation. The tool should follow Docker/Kubernetes-style architecture with extensible plugin system and comprehensive code generation capabilities.

## Architecture Requirements

### Core System Architecture

```
saturday/
├── cmd/
│   ├── saturday/                # Main CLI binary
│   │   └── main.go
│   └── plugins/                 # Example plugin commands
│       ├── example-plugin/
│       └── custom-validator/
├── internal/
│   ├── commands/                # Cobra command implementations
│   │   ├── generate.go
│   │   ├── init.go
│   │   ├── plugin.go
│   │   └── version.go
│   ├── generator/               # Code generation engine
│   │   ├── types.go
│   │   ├── base_generator.go
│   │   ├── site_generator.go
│   │   ├── page_generator.go
│   │   ├── flow_generator.go
│   │   ├── element_generator.go
│   │   ├── tool_generator.go
│   │   ├── command_generator.go
│   │   └── framework_generator.go
│   ├── templates/               # Template system
│   │   ├── templates.go
│   │   ├── framework_templates.go
│   │   ├── ml_templates.go
│   │   └── config_templates.go
│   ├── plugin/                  # Plugin management system
│   │   ├── manager.go
│   │   ├── interface.go
│   │   └── binary_plugin.go
│   ├── config/                  # Configuration management
│   │   └── config.go
│   └── utils/                   # Utility functions
│       ├── file_utils.go
│       ├── string_utils.go
│       └── validation.go
├── pkg/                         # Public APIs for plugins
│   └── plugin/
│       └── interface.go
├── docs/                        # Comprehensive documentation
├── examples/                    # Example projects and plugins
├── scripts/                     # Build and deployment scripts
├── .github/workflows/           # CI/CD pipelines
├── Makefile                     # Build automation
├── Dockerfile                   # Container support
├── go.mod
├── go.sum
└── README.md
```

## Core CLI Requirements

### 1. Main CLI Application (`cmd/saturday/main.go`)

**Requirements:**
- Use **Cobra** framework for command structure
- Support version info with git commit, build date
- Global flags: `--config`, `--verbose`, `--dry-run`
- Plugin discovery and loading on startup
- Comprehensive error handling and user feedback
- Support for shell completion (bash, zsh, fish)

**Features:**
```bash
saturday --version               # Show version info
saturday --help                  # Show help
saturday completion bash         # Generate shell completion
```

### 2. Command Structure (`internal/commands/`)

#### Generate Commands (`generate.go`)
```bash
# Framework scaffolding
saturday generate framework [NAME] --with-ml --with-examples

# Site generation
saturday generate site [SITE_NAME] --base-url URL --with-ml --with-factories

# Page generation
saturday generate page [PAGE_NAME] --site SITE --path PATH --container SELECTOR

# Flow generation
saturday generate flow [FLOW_NAME] --site SITE --pages "PAGE1,PAGE2,PAGE3"

# Element generation
saturday generate element [ELEMENT_NAME] --base-type TYPE --methods "method1,method2"

# Tool generation
saturday generate tool [TOOL_NAME] --type TYPE --description DESC

# Plugin generation
saturday generate command [COMMAND_NAME] --cobra --description DESC --author AUTHOR
```

#### Init Commands (`init.go`)
```bash
saturday init project [NAME]     # Initialize complete project
saturday init config             # Create ~/.saturday.yaml config
```

#### Plugin Commands (`plugin.go`)
```bash
saturday plugin list             # List installed plugins
saturday plugin install URL/PATH # Install plugin
saturday plugin uninstall NAME   # Remove plugin
saturday plugin info NAME        # Show plugin details
```

## Generator System Requirements

### 1. Base Generator (`internal/generator/base_generator.go`)

**Interface:**
```go
type Generator interface {
    Generate(config interface{}) error
    Validate(config interface{}) error
    GetTemplates() map[string]string
}

type baseGenerator struct {
    templateEngine *template.Template
    dryRun        bool
    verbose       bool
}
```

**Methods:**
- Template parsing and execution
- File and directory creation
- Validation and error handling
- Progress reporting
- Dry-run support

### 2. Framework Generator

**Generated Structure:**
```
project-name/
├── features/
│   ├── hooks/playwright/
│   │   ├── browser-options.ts
│   │   ├── hooks.ts
│   │   └── setup-playwright.ts
│   ├── playwright/
│   │   └── console-logger.ts
│   ├── snippets/
│   │   ├── README.md
│   │   └── ts-snippets-syntax.js
│   ├── world/
│   │   ├── custom-world.ts
│   │   └── custom-world-options.ts
│   └── step_definitions/
├── lib/
│   ├── framework/
│   │   ├── base/
│   │   │   ├── base-element.ts
│   │   │   ├── base-filter.ts
│   │   │   ├── base-flow.ts
│   │   │   ├── base-page.ts
│   │   │   └── base-site.ts
│   │   ├── elements/
│   │   │   ├── button.ts
│   │   │   ├── input.ts
│   │   │   └── link.ts
│   │   └── ml/                  # Optional ML framework
│   │       ├── base/
│   │       ├── detectors/
│   │       ├── models/
│   │       ├── trainers/
│   │       ├── facades/
│   │       └── types/
│   ├── sites/
│   └── tools/
├── reports/
├── package.json
├── tsconfig.json
├── cucumber.js
├── eslint.config.mjs
└── README.md
```

### 3. Site Generator

**Generated for each site:**
```
lib/sites/SITE_NAME/
├── SITE_NAME.ts                 # Main site class
├── factories/
├── filters/
├── flows/
├── hooks/
├── pages/
│   └── landing.page.ts         # Default landing page
├── models/
└── interfaces/
```

### 4. Page/Flow/Element Generators

**Requirements:**
- TypeScript class generation with proper imports
- Integration with base framework classes
- Validation of dependencies (site must exist for pages)
- Support for custom templates and overrides

## Template System Requirements

### 1. Template Engine (`internal/templates/`)

**Features:**
- Go template system with custom functions
- Helper functions: `toPascalCase`, `toCamelCase`, `toKebabCase`
- Conditional template sections
- Template inheritance and composition
- Template validation and error reporting

**Template Types:**
- **TypeScript Templates**: Pages, flows, elements, site classes
- **Configuration Templates**: package.json, tsconfig.json, cucumber.js
- **Documentation Templates**: README.md files
- **ML Templates**: Complete ML framework integration

### 2. ML Framework Templates

**Based on uploaded project files:**
- All ML types and interfaces from `ml-types.ts`
- Base classes: `BaseDetector`, `BaseModel`, `BaseTrainer`
- Facades: `DetectorsFacade`, `ModelsFacade`, `TrainersFacade`
- Complete detector implementations
- Model and trainer implementations
- Configuration and utility classes

## Plugin System Requirements

### 1. Plugin Manager (`internal/plugin/manager.go`)

**Features:**
- **Auto-discovery**: Search multiple paths for plugins
- **Go Plugin Support**: Load `.so` shared libraries
- **Binary Plugin Support**: Execute `taza-*` binaries
- **Plugin Registration**: Register commands with root CLI
- **Error Handling**: Graceful failure for missing/broken plugins
- **Plugin Info**: Query plugin metadata

**Plugin Paths:**
- `./plugins/` (local development)
- `./bin/` (project binaries)
- `/usr/local/bin/` (system-wide)
- `$SATURDAY_PLUGIN_PATH` (environment variable)
- Config file specified paths

### 2. Plugin Interface

**Go Plugin Interface:**
```go
type PluginInterface interface {
    Name() string
    Description() string
    Version() string
    Command() *cobra.Command
}
```

**Binary Plugin Requirements:**
- Executable named `saturday-PLUGIN_NAME`
- Support `--info` flag for metadata
- Standard input/output handling
- Exit codes and error reporting

## Configuration System Requirements

### 1. Configuration Management (`internal/config/config.go`)

**Using Viper for:**
- YAML configuration files
- Environment variable support
- Command-line flag integration
- Configuration hierarchy

**Default Configuration (`~/.saturday.yaml`):**
```yaml
generator:
  author: ""
  license: "MIT"
  defaults:
    ml: true
    examples: true
    factories: true

plugin:
  paths:
    - ./plugins
    - ./bin
    - /usr/local/bin

output:
  verbose: false
  colors: true

sites:
  default:
    baseUrl: "https://example.com"
    timeout: 30000
    headless: true

ml:
  modelsPath: "./ml-models"
  dataPath: "./training-data"
  logsPath: "./logs"
  reportsPath: "./reports"
```

## Build System Requirements

### 1. Makefile

**Targets:**
```makefile
build                   # Build main binary
build-all              # Cross-platform builds
test                    # Run tests
test-coverage          # Run tests with coverage
lint                   # Code linting
clean                  # Clean build artifacts
install                # Install to GOPATH/bin
install-system         # Install to /usr/local/bin
release                # Create release packages
docker-build           # Build Docker image
examples               # Generate example projects
plugin-example         # Build example plugin
docs                   # Generate documentation
```

### 2. Cross-Platform Support

**Build Targets:**
- Linux (amd64, arm64)
- macOS (amd64, arm64)
- Windows (amd64)
- Docker container support

### 3. CI/CD Pipeline (`.github/workflows/`)

**Workflows:**
- **CI**: Test, lint, security scan on multiple Go versions
- **Release**: Automated releases with GitHub Actions
- **Documentation**: Auto-generate and publish docs

## Documentation Requirements

### 1. User Documentation

**Structure:**
```
docs/
├── getting-started.md
├── installation.md
├── cli-reference.md
├── generators/
│   ├── framework.md
│   ├── site.md
│   ├── page.md
│   └── flow.md
├── plugins/
│   ├── development.md
│   ├── go-plugins.md
│   └── binary-plugins.md
├── ml-framework/
│   ├── overview.md
│   ├── training.md
│   └── validation.md
├── examples/
├── architecture.md
└── contributing.md
```

### 2. API Documentation

**Requirements:**
- Go package documentation with examples
- Plugin development guide
- Template system documentation
- Configuration reference

### 3. Examples

**Example Projects:**
- Basic Playwright framework
- E-commerce site testing
- ML-powered visual validation
- Custom plugin development

## Testing Requirements

### 1. Unit Tests

**Coverage:**
- All generators with mock file system
- Template system validation
- Plugin system functionality
- Configuration management
- Utility functions

### 2. Integration Tests

**Test Scenarios:**
- End-to-end project generation
- Plugin loading and execution
- Template rendering with real data
- Cross-platform functionality

### 3. Example Validation

**Automated Testing:**
- Generated projects build successfully
- TypeScript compilation passes
- Cucumber tests execute
- ML framework loads correctly

## Quality Requirements

### 1. Code Quality

**Standards:**
- **golangci-lint** with comprehensive rules
- **gosec** security scanning
- **gofmt** code formatting
- **Test coverage** > 80%
- **Documentation coverage** for public APIs

### 2. Error Handling

**Requirements:**
- Comprehensive error messages
- Helpful suggestions for common issues
- Graceful degradation for missing dependencies
- Clear validation error reporting

### 3. Performance

**Benchmarks:**
- Framework generation < 5 seconds
- Template rendering < 1 second
- Plugin loading < 500ms
- Memory usage optimization

## Security Requirements

### 1. Plugin Security

**Measures:**
- Plugin signature verification (future)
- Sandboxed plugin execution
- Permission-based access control
- Security scanning of plugin code

### 2. Code Generation Security

**Validation:**
- Input sanitization for all user inputs
- Path traversal prevention
- Template injection protection
- File permission management

## Extensibility Requirements

### 1. Template Customization

**Features:**
- User-defined template directories
- Template override mechanism
- Custom helper functions
- Template validation

### 2. Generator Plugins

**Architecture:**
- Plugin-based generator system
- Custom generator registration
- Generator dependency management
- Generator configuration schema

### 3. Output Format Support

**Extensibility:**
- Multiple target languages (future)
- Different test frameworks (future)
- Custom output formats
- Template engine alternatives

## Success Criteria

### 1. Functional Requirements

- [ ] Complete framework generation in < 30 seconds
- [ ] All generated TypeScript code compiles without errors
- [ ] Plugin system supports both Go and binary plugins
- [ ] ML framework integration works seamlessly
- [ ] Cross-platform builds and distributions
- [ ] Comprehensive CLI help and documentation

### 2. Quality Requirements

- [ ] Test coverage > 80%
- [ ] All linting rules pass
- [ ] Security scan passes
- [ ] Performance benchmarks met
- [ ] Documentation complete and accurate
- [ ] Example projects demonstrate all features

### 3. User Experience Requirements

- [ ] Intuitive command structure
- [ ] Clear error messages and suggestions
- [ ] Helpful documentation and examples
- [ ] Fast execution times
- [ ] Reliable cross-platform operation
- [ ] Easy plugin development process

## Implementation Phases

### Phase 1: Core CLI and Basic Generators
1. Set up project structure and build system
2. Implement core CLI with Cobra
3. Create basic generators (framework, site, page)
4. Implement template system
5. Add configuration management

### Phase 2: Plugin System and Advanced Generators
1. Implement plugin manager and interfaces
2. Add remaining generators (flow, element, tool, command)
3. Create example plugins
4. Add comprehensive testing

### Phase 3: ML Framework Integration
1. Implement ML template system
2. Generate complete ML framework
3. Add ML-specific generators and commands
4. Integrate with main framework

### Phase 4: Polish and Documentation
1. Comprehensive documentation
2. Performance optimization
3. Security hardening
4. Cross-platform testing and release automation
5. Community features and plugin ecosystem

## Deliverables

1. **Complete Go CLI Application** with all specified features
2. **Comprehensive Documentation** including user guides and API docs
3. **Build and Release System** with cross-platform support
4. **Example Projects** demonstrating all capabilities
5. **Plugin Development Kit** with examples and documentation
6. **Test Suite** with >80% coverage
7. **Docker Support** for containerized usage
8. **CI/CD Pipeline** for automated testing and releases

This comprehensive prompt provides complete specifications for building a sophisticated, production-ready CLI tool that can generate and manage complex test automation frameworks while supporting extensibility through a robust plugin system.