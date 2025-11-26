# Submodules Development Guide

## Overview

Submodules are complete Ruby gems developed as separate git repositories and linked into the active_data_flow repository. They package specific functionality (connectors, runtimes) as independent gems with their own version control.

**Key Distinction**: Submodules ARE git submodules - they are separate repositories linked into active_data_flow.

## Why Submodules?

- **Modular Development**: Each component can be developed independently
- **Independent Versioning**: Submodules have their own version numbers and git history
- **Selective Installation**: Users install only the submodules they need
- **Independent Repositories**: Each component has its own git repository
- **Turnkey Installation**: Common use-cases work out of the box

## Submodule Structure

Each submodule follows this standard structure:

```
submodules/active_data_flow-{component}-{type}-{name}/
├── lib/
│   ├── active_data_flow/
│   │   └── {component}/
│   │       └── {type}/
│   │           ├── {name}.rb           # Main implementation
│   │           └── {name}/
│   │               └── version.rb      # Version constant
│   └── active_data_flow-{component}-{type}-{name}.rb  # Entry point
├── spec/
│   ├── spec_helper.rb
│   └── {name}_spec.rb
├── .kiro/
│   ├── specs/
│   │   ├── requirements.md             # Subgem-specific requirements
│   │   ├── design.md                   # Implementation design
│   │   ├── tasks.md                    # Implementation tasks
│   │   ├── parent_requirements.md      # Symlink to parent
│   │   └── parent_design.md            # Symlink to parent
│   ├── steering/                       # All symlinks to parent
│   │   ├── glossary.md
│   │   ├── product.md
│   │   ├── structure.md
│   │   ├── tech.md
│   │   ├── design_gem.md
│   │   ├── dry.md
│   │   ├── test_driven_design.md
│   │   └── gemfiles.md
│   ├── settings/
│   └── README.md
├── active_data_flow-{component}-{type}-{name}.gemspec
├── Gemfile
├── Rakefile
├── README.md
├── CHANGELOG.md
├── .gitignore
└── .rspec
```

## Naming Conventions

### Gem Names
- Pattern: `active_data_flow-{component}-{type}-{name}`
- Examples:
  - `active_data_flow-connector-source-active_record`
  - `active_data_flow-connector-sink-active_record`
  - `active_data_flow-runtime-heartbeat`

### Module Names
- Pattern: `ActiveDataFlow::{Component}::{Type}::{Name}`
- Examples:
  - `ActiveDataFlow::Connector::Source::ActiveRecord`
  - `ActiveDataFlow::Connector::Sink::ActiveRecord`
  - `ActiveDataFlow::Runtime::Heartbeat`

### File Paths
Match module structure:
- `lib/active_data_flow/connector/source/active_record.rb`
- `lib/active_data_flow/connector/sink/active_record.rb`
- `lib/active_data_flow/runtime/heartbeat.rb`

## Current Submodules

### Connectors

**Source Connectors** (read data from external systems):
- `active_data_flow-connector-source-active_record` - Read from database tables

**Sink Connectors** (write data to external systems):
- `active_data_flow-connector-sink-active_record` - Write to database tables

### Runtimes

**Execution Environments**:
- `active_data_flow-runtime-heartbeat` - Rails engine with periodic HTTP-triggered execution

## Creating a New Submodule

### 1. Create Repository

Create a new GitHub repository for the submodule, then clone it into submodules/:

```bash
cd submodules
git clone https://github.com/yourusername/active_data_flow-{component}-{type}-{name}.git
cd active_data_flow-{component}-{type}-{name}
mkdir -p lib/active_data_flow/{component}/{type} spec .kiro/{specs,steering,settings}
```

### 2. Core Files

Create these essential files:
- `{name}.gemspec` - Gem specification
- `Gemfile` - Development dependencies
- `Rakefile` - Build tasks
- `README.md` - Usage documentation
- `CHANGELOG.md` - Version history
- `.gitignore` - Git exclusions
- `.rspec` - RSpec configuration

### 3. Implementation Files

- `lib/active_data_flow/{component}/{type}/{name}.rb` - Main implementation
- `lib/active_data_flow/{component}/{type}/{name}/version.rb` - Version constant
- `lib/active_data_flow-{component}-{type}-{name}.rb` - Entry point

### 4. .kiro Structure

**Create symlinks to parent steering**:
```bash
cd .kiro/steering
ln -sf ../../../.kiro/glossary.md glossary.md
ln -sf ../../../.kiro/steering/product.md product.md
ln -sf ../../../.kiro/steering/structure.md structure.md
ln -sf ../../../.kiro/steering/tech.md tech.md
ln -sf ../../../.kiro/steering/design_gem.md design_gem.md
ln -sf ../../../.kiro/steering/dry.md dry.md
ln -sf ../../../.kiro/steering/test_driven_design.md test_driven_design.md
ln -sf ../../../.kiro/steering/gemfiles.md gemfiles.md
```

**Create symlinks to parent specs**:
```bash
cd ../ specs
ln -sf ../../../.kiro/specs/requirements.md parent_requirements.md
ln -sf ../../../.kiro/specs/design.md parent_design.md
```

**Create submodule-specific docs**:
- `.kiro/specs/requirements.md` - Submodule requirements
- `.kiro/specs/design.md` - Implementation design
- `.kiro/specs/tasks.md` - Implementation tasks
- `.kiro/README.md` - .kiro structure guide

### 5. Gemfile Template

See: `.kiro/steering/gemfiles.md` for complete Gemfile guidelines

```ruby
# frozen_string_literal: true

source 'https://rubygems.org'

git_source(:github) { |repo| "https://github.com/#{repo}.git" }

# Submoduler child gem
gem 'submoduler-core-submoduler_child', git: 'https://github.com/magenticmarketactualskill/submoduler-core-submoduler_child.git'

gemspec
```

### 6. Gemspec Template

```ruby
# frozen_string_literal: true

require_relative "lib/active_data_flow/{component}/{type}/{name}/version"

Gem::Specification.new do |spec|
  spec.name          = "active_data_flow-{component}-{type}-{name}"
  spec.version       = ActiveDataFlow::{Component}::{Type}::{Name}::VERSION
  spec.authors       = ["ActiveDataFlow Team"]
  spec.email         = ["team@example.com"]

  spec.summary       = "Brief description"
  spec.description   = "Detailed description"
  spec.homepage      = "https://github.com/yourusername/active_data_flow"
  spec.license       = "MIT"
  spec.required_ruby_version = ">= 2.7.0"

  spec.files = Dir["lib/**/*", "README.md", "LICENSE.txt", "CHANGELOG.md"]
  spec.require_paths = ["lib"]

  # Core dependency
  spec.add_dependency "active_data_flow", "~> 0.1"
  
  # Component-specific dependencies
  # spec.add_dependency "activerecord", ">= 6.0"

  # Development dependencies
  spec.add_development_dependency "bundler", "~> 2.0"
  spec.add_development_dependency "rake", "~> 13.0"
  spec.add_development_dependency "rspec", "~> 3.0"
end
```

## Development Workflow

### Local Development

1. **Work in submodule directory**:
   ```bash
   cd submodules/active_data_flow-connector-source-active_record
   bundle install
   bundle exec rspec
   ```

2. **Commit and push changes**:
   ```bash
   git add .
   git commit -m "Your changes"
   git push
   ```

3. **Update parent to reference new commit**:
   ```bash
   cd ../..  # Back to active_data_flow root
   git add submodules/active_data_flow-connector-source-active_record
   git commit -m "Update submodule to latest"
   git push
   ```

### Publishing

1. **Update version** in `lib/active_data_flow/{component}/{type}/{name}/version.rb`
2. **Update CHANGELOG.md**
3. **Build gem**: `gem build active_data_flow-{component}-{type}-{name}.gemspec`
4. **Publish**: `gem push active_data_flow-{component}-{type}-{name}-{version}.gem`

## Best Practices

### Code Organization

- **Single Responsibility**: Each submodule does one thing well
- **Minimal Dependencies**: Only depend on what's necessary
- **Clear Interfaces**: Follow parent abstract base classes
- **Comprehensive Tests**: Test all public interfaces

### Documentation

- **README**: Clear usage examples
- **CHANGELOG**: Document all changes
- **Requirements**: EARS-formatted acceptance criteria
- **Design**: Architecture and implementation details
- **Tasks**: Implementation checklist

### Version Management

- **Semantic Versioning**: MAJOR.MINOR.PATCH
- **Independent Versions**: Each submodule versions independently
- **Compatibility**: Document compatibility with core gem versions

## Troubleshooting

### Bundle Issues

If `bundle install` fails in a submodule:
1. Check Gemfile has correct submoduler_child reference
2. Verify gemspec dependencies are correct
3. Try `bundle update` to refresh dependencies

### Symlink Issues

If symlinks are broken:
1. Verify path depth (should be `../../../` for submodules)
2. Check parent files exist
3. Recreate symlinks with correct paths

### Submodule Update Issues

If submodule is out of sync:
1. `git submodule update --init --recursive` to initialize
2. `cd submodules/{name} && git pull` to update
3. `cd ../.. && git add submodules/{name}` to commit reference

### Import Issues

If Ruby can't find modules:
1. Check file paths match module structure
2. Verify entry point requires main implementation
3. Check gemspec `require_paths` includes "lib"

## Related Documentation

- **Gem Design**: `.kiro/steering/design_gem.md`
- **Gemfile Guidelines**: `.kiro/steering/gemfiles.md`
- **Project Structure**: `.kiro/steering/structure.md`
- **Technology Stack**: `.kiro/steering/tech.md`
