# Submodules Guide

## Overview

All component implementations (connectors, runtimes) are now managed as git submodules. Each submodule is a separate git repository linked into the active_data_flow project.

**Current Structure**: All components are in `submodules/` directory as git submodules with independent repositories.

## Vendor Directory

The `vendor/` directory contains vendored copies of the submoduler gems for local development:

- **`vendor/submoduler_parent/`** - Submoduler parent gem (used by active_data_flow)
- **`vendor/submoduler/`** - Base submoduler functionality

These are git clones of the submoduler repositories, allowing local development without depending on remote git repositories. The Gemfile references these via git URLs, but they can be overridden to use local paths during development.

## Current Submodules

All component implementations are now managed as submodules:

**Connectors**:
- `submodules/active_data_flow-connector-source-active_record/` - ActiveRecord source connector
- `submodules/active_data_flow-connector-sink-active_record/` - ActiveRecord sink connector

**Runtimes**:
- `submodules/active_data_flow-runtime-heartbeat/` - Rails heartbeat runtime

**Examples**:
- `submodules/examples/rails8-demo/` - Example Rails 8 application

**Note**: Check `.gitmodules` file for current submodule list.

## Working with Submodules

### Cloning Repository with Submodules

```bash
# Clone with submodules
git clone --recursive https://github.com/yourusername/active_data_flow.git

# Or if already cloned
git submodule update --init --recursive
```

### Adding a New Submodule

```bash
# Add submodule
git submodule add https://github.com/org/repo.git submodules/component-name

# Commit the change
git add .gitmodules submodules/component-name
git commit -m "Add component-name submodule"
```

### Updating Submodules

```bash
# Update all submodules to latest
git submodule update --remote

# Update specific submodule
cd submodules/component-name
git pull origin main
cd ../..
git add submodules/component-name
git commit -m "Update component-name submodule"
```

### Removing a Submodule

```bash
# Remove submodule
git submodule deinit -f submodules/component-name
git rm -f submodules/component-name
rm -rf .git/modules/submodules/component-name
git commit -m "Remove component-name submodule"
```

## Submodule Structure

Submodules follow this standard structure:

```
submodules/active_data_flow-{component}-{type}-{name}/
├── lib/
│   └── active_data_flow/
│       └── {component}/
│           └── {type}/
│               └── {name}.rb
├── spec/
├── .kiro/
│   ├── specs/
│   └── steering/
├── active_data_flow-{component}-{type}-{name}.gemspec
├── Gemfile
├── README.md
└── .git/  # Separate git repository
```

## Development Workflow

### Working on Submodule Code

1. **Navigate to submodule**:
   ```bash
   cd submodules/component-name
   ```

2. **Create branch**:
   ```bash
   git checkout -b feature/my-feature
   ```

3. **Make changes and commit**:
   ```bash
   git add .
   git commit -m "Add feature"
   git push origin feature/my-feature
   ```

4. **Update parent repository**:
   ```bash
   cd ../..  # Back to active_data_flow root
   git add submodules/component-name
   git commit -m "Update component-name to include new feature"
   ```

### Testing Submodule Changes

```bash
# In parent repository
cd submodules/component-name
bundle install
bundle exec rspec

# Test integration with parent
cd ../..
bundle install
bundle exec rspec
```

## Gemfile Configuration

### Parent Gem (active_data_flow)

The parent gem uses `submoduler_parent`:

```ruby
# frozen_string_literal: true

source 'https://rubygems.org'

git_source(:github) { |repo| "https://github.com/#{repo}.git" }

# Submoduler parent gem
gem 'submoduler-core-submoduler_parent', git: 'https://github.com/magenticmarketactualskill/submoduler-core-submoduler_child.git'
```

**Local Development Override**:
```ruby
# Use vendored copy for local development
gem 'submoduler-core-submoduler_parent', path: 'vendor/submoduler_parent'
```

### Submodules

All submodules use `submoduler_child`:

```ruby
# frozen_string_literal: true

source 'https://rubygems.org'

git_source(:github) { |repo| "https://github.com/#{repo}.git" }

# Submoduler child gem
gem 'submoduler-core-submoduler_child', git: 'https://github.com/magenticmarketactualskill/submoduler-core-submoduler_child.git'

gemspec
```

**Local Development Override**:
```ruby
# Use vendored copy for local development
gem 'submoduler-core-submoduler_child', path: '../../../vendor/submoduler_child'
```

See: `.kiro/steering/gemfiles.md` for complete Gemfile guidelines

## .gitmodules File

The `.gitmodules` file tracks submodule configuration:

```ini
[submodule "submodules/component-name"]
    path = submodules/component-name
    url = https://github.com/org/repo.git
    branch = main
```

## Best Practices

### Repository Management

- **Keep submodules updated**: Regularly update to latest versions
- **Document dependencies**: Note which submodules are required vs optional
- **Pin versions**: Commit specific submodule commits, not floating branches
- **Test integration**: Always test parent repo after updating submodules

### Development

- **Branch in submodule**: Create feature branches in submodule repo
- **PR workflow**: Use pull requests in submodule repo
- **Update parent**: After merging submodule PR, update parent repo
- **Coordinate releases**: Plan releases between submodule and parent

### Documentation

- **README in submodule**: Document usage and development
- **CHANGELOG**: Track changes in submodule repo
- **Integration docs**: Document how submodule integrates with parent

## Troubleshooting

### Submodule Not Initialized

```bash
git submodule update --init --recursive
```

### Submodule Detached HEAD

```bash
cd submodules/component-name
git checkout main
git pull
cd ../..
git add submodules/component-name
git commit -m "Update submodule to latest main"
```

### Submodule Conflicts

```bash
# Reset submodule to committed version
git submodule update --force

# Or update to latest
cd submodules/component-name
git fetch
git checkout origin/main
cd ../..
git add submodules/component-name
git commit -m "Resolve submodule conflict"
```

### Bundle Issues

If bundle fails in submodule:
1. Check submodule is initialized: `git submodule status`
2. Update submodule: `git submodule update --remote`
3. Check Gemfile has correct dependencies
4. Try `bundle update` in submodule directory

## Adding New Submodules

### Creating a New Submodule

1. Create new git repository for component
2. Initialize the component code in the new repo
3. Add as submodule to `submodules/`
4. Update parent Gemfile references
5. Update `.submoduler.ini` configuration

## Ignoring Submodules

For development or CI where submodules aren't needed:

```bash
# Skip submodule initialization
git clone https://github.com/yourusername/active_data_flow.git
# Don't run: git submodule update --init
```

The active_data_flow gem requires submodules to be initialized for full functionality.

## Working with Vendor Directory

### Updating Vendored Submoduler Gems

```bash
# Update submoduler_parent
cd vendor/submoduler_parent
git pull origin main
cd ../..

# Update submoduler_child
cd vendor/submoduler_child
git pull origin main
cd ../..

# Commit the updates
git add vendor/
git commit -m "Update vendored submoduler gems"
```

### Using Local Vendor Copies

To use the vendored copies instead of git URLs, temporarily modify your Gemfile:

**Parent Gemfile**:
```ruby
# gem 'submoduler-core-submoduler_parent', git: 'https://github.com/...'
gem 'submoduler-core-submoduler_parent', path: 'vendor/submoduler_parent'
```

**Subgem Gemfile**:
```ruby
# gem 'submoduler-core-submoduler_child', git: 'https://github.com/...'
gem 'submoduler-core-submoduler_child', path: '../../../vendor/submoduler_child'
```

Then run:
```bash
bundle install
```

**Important**: Don't commit these path changes - they're for local development only.

### Vendor Directory Structure

```
vendor/
├── submoduler_parent/          # Parent gem (for active_data_flow)
│   ├── .git/                   # Git repository
│   ├── lib/
│   ├── submoduler_parent.gemspec
│   └── README.md
├── submoduler_child/           # Child gem (for submodules)
│   ├── .git/                   # Git repository
│   ├── lib/
│   ├── submoduler_child.gemspec
│   └── README.md
└── submoduler/                 # Base submoduler
    ├── .git/                   # Git repository
    ├── lib/
    └── README.md
```

## Related Documentation

- **Submodules Development**: `.kiro/steering/submodules_development.md` - Guide for developing submodules
- **Gemfile Guidelines**: `.kiro/steering/gemfiles.md`
- **Project Structure**: `.kiro/steering/structure.md`
- **Git Submodules**: https://git-scm.com/book/en/v2/Git-Tools-Submodules