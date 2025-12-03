# Phase 4 Test: Workspace with Multiple UV Version Features

## Test ID
T-P4-013

## Category
Version Compatibility - Multi-Version UV Workspace

## Priority
P1

## Description
This fixture tests a workspace where different services use features from different UV versions, demonstrating UV's evolution and backwards compatibility across version ranges.

## UV Version Evolution Context

### UV Version Timeline:
- **UV 0.1.x - 0.4.x** (Early 2024): Basic package management, initial workspace support
- **UV 0.5.x - 0.7.x** (Mid 2024): Dependency groups, improved workspace features
- **UV 0.8.x - 0.9.x+** (Late 2024): PEP 735 full support, advanced features

### Why Test Multiple UV Versions:
- Teams upgrade UV incrementally
- Different services may use different UV features
- Need to ensure workspace compatibility across UV versions
- Verify backwards compatibility
- Test migration paths

## Real-World Scenario

### Enterprise Environment:
```bash
# Different teams using different UV versions
Team A: uv 0.4.12 (legacy)
Team B: uv 0.7.5 (mid-range)
Team C: uv 0.9.0+ (latest)

# All services must work together in one workspace
# CI/CD may use latest UV, but developers use various versions
```

## Test Objectives

1. **Verify workspace compatibility** across UV versions
2. **Test legacy configuration formats** still work
3. **Demonstrate evolution** of UV features
4. **Ensure backwards compatibility**
5. **Document migration paths** between versions

## File Structure

```
workspace_multiple_uv_versions/
├── pyproject.toml              # Root workspace
├── README.md                   # This file
└── packages/
    ├── service-a/              # UV 0.4.x compatible
    │   ├── pyproject.toml
    │   └── README.md
    ├── service-b/              # UV 0.7.x features
    │   ├── pyproject.toml
    │   └── README.md
    └── service-c/              # UV 0.9.x+ features
        ├── pyproject.toml
        └── README.md
```

## Root Workspace Configuration

```toml
[project]
name = "workspace-multiple-uv-versions"
version = "1.0.0"
description = "Test: Workspace with services using different UV version features"
requires-python = ">=3.10"

[tool.uv.workspace]
members = ["packages/*"]

[dependency-groups]
dev = [
    "pytest>=8.0.0",
    "mypy>=1.8.0",
    "ruff>=0.3.0",
]
```

## Service Configurations

### Service A: Legacy UV 0.4.x Compatible
**UV Version**: 0.4.x compatible
**Features Used**:
- Basic `[tool.uv]` section
- Legacy dev-dependencies format
- Simple workspace member

```toml
# packages/service-a/pyproject.toml
[project]
name = "service-a-legacy"
version = "1.0.0"
requires-python = ">=3.10"

dependencies = [
    "fastapi>=0.109.0",
    "uvicorn>=0.27.0",
]

# Legacy UV 0.4.x format (still works but deprecated)
[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
]
```

### Service B: Mid-Range UV 0.7.x Features
**UV Version**: 0.7.x features
**Features Used**:
- Modern `[dependency-groups]` format (PEP 735)
- Workspace internal dependencies
- `[tool.uv.sources]` for workspace members

```toml
# packages/service-b/pyproject.toml
[project]
name = "service-b-modern"
version = "1.0.0"
requires-python = ">=3.10"

dependencies = [
    "fastapi>=0.109.0",
    "pydantic>=2.6.0",
    "service-a-legacy",  # Depends on service-a
]

# Modern dependency groups (UV 0.7+)
[dependency-groups]
dev = [
    "pytest>=8.0.0",
    "pytest-asyncio>=0.23.0",
]

[tool.uv.sources]
service-a-legacy = { workspace = true }
```

### Service C: Latest UV 0.9.x+ Features
**UV Version**: 0.9.x+ latest
**Features Used**:
- Full PEP 735 dependency groups
- Multiple dependency groups
- Latest workspace features
- Optional dependencies with groups

```toml
# packages/service-c/pyproject.toml
[project]
name = "service-c-latest"
version = "1.0.0"
requires-python = ">=3.10"

dependencies = [
    "fastapi>=0.115.0",
    "pydantic>=2.9.0",
    "service-a-legacy",
    "service-b-modern",
]

[project.optional-dependencies]
ml = [
    "torch>=2.1.0",
    "transformers>=4.36.0",
]

# Latest dependency groups with multiple groups
[dependency-groups]
dev = [
    "pytest>=8.3.0",
    "pytest-asyncio>=0.24.0",
    "pytest-xdist>=3.5.0",
]

test = [
    "pytest>=8.3.0",
    "pytest-cov>=6.0.0",
    "coverage[toml]>=7.4.0",
]

lint = [
    "ruff>=0.7.0",
    "mypy>=1.13.0",
    "pyright>=1.1.380",
]

[tool.uv.sources]
service-a-legacy = { workspace = true }
service-b-modern = { workspace = true }
```

## UV Version Features Comparison

### UV 0.4.x (Legacy):
```toml
# Old format (still works)
[tool.uv]
dev-dependencies = ["pytest>=7.0.0"]
```

### UV 0.7.x (Transition):
```toml
# New format (PEP 735 initial support)
[dependency-groups]
dev = ["pytest>=8.0.0"]
```

### UV 0.9.x+ (Modern):
```toml
# Multiple dependency groups
[dependency-groups]
dev = ["pytest>=8.3.0"]
test = ["pytest-cov>=6.0.0"]
lint = ["ruff>=0.7.0"]
```

## Expected Behavior

### With UV 0.9.x+ (Latest):
```bash
$ uv --version
uv 0.9.0

# Should work with all services
$ uv lock
Using CPython 3.10.12
Resolved 75 packages in 1.2s

$ uv sync
Installed 75 packages in 2.5s

# All services accessible
$ uv run python -c "import service_a_legacy; import service_b_modern; import service_c_latest"
# Success
```

### With UV 0.7.x (Mid-Range):
```bash
$ uv --version
uv 0.7.5

# Should work, may show deprecation warnings
$ uv lock
⚠️  Warning: The `tool.uv.dev-dependencies` field is deprecated
    Use `dependency-groups` instead

Resolved 75 packages in 1.4s

$ uv sync
Installed 75 packages in 2.8s
```

### With UV 0.4.x (Legacy):
```bash
$ uv --version
uv 0.4.12

# May not support all features
$ uv lock
error: Unknown field `dependency-groups`
  Expected one of: dependencies, optional-dependencies, ...

# Recommendation: Upgrade to UV 0.7+
```

## Test Scenarios

### Scenario 1: Workspace Lock File Generation
```bash
# Latest UV should handle all versions
$ uv lock
# Should generate single lock file for entire workspace
# Should resolve all internal dependencies correctly
```

### Scenario 2: Backwards Compatibility
```bash
# Service A uses legacy format
# Service B and C use modern format
# All should work together in workspace
$ uv sync --package service-a-legacy
$ uv sync --package service-b-modern
$ uv sync --package service-c-latest
```

### Scenario 3: Dependency Groups
```bash
# Service C has multiple dependency groups
$ uv sync --group dev
$ uv sync --group test
$ uv sync --group lint

# Service A uses legacy dev-dependencies
# Should still install dev dependencies
```

### Scenario 4: Internal Dependencies
```bash
# Service C depends on B depends on A
# Should resolve correctly regardless of UV version
$ uv tree
service-c-latest
├── service-b-modern
│   └── service-a-legacy
├── fastapi
└── pydantic
```

## Success Criteria

- [ ] Workspace locks successfully with latest UV
- [ ] All three services build correctly
- [ ] Internal dependencies resolve properly
- [ ] Legacy format still works
- [ ] Modern features work in latest service
- [ ] No conflicts between version-specific features
- [ ] Deprecation warnings shown for legacy format
- [ ] Migration path clear

## Migration Path

### From UV 0.4.x to 0.7.x+:
```bash
# Step 1: Update pyproject.toml
# Change from:
[tool.uv]
dev-dependencies = ["pytest>=7.0.0"]

# To:
[dependency-groups]
dev = ["pytest>=8.0.0"]

# Step 2: Update UV
pip install --upgrade uv

# Step 3: Regenerate lock file
uv lock --upgrade

# Step 4: Test
uv sync && pytest
```

### From UV 0.7.x to 0.9.x+:
```bash
# Already using modern format
# Just upgrade UV version
pip install --upgrade uv

# Benefit from performance improvements
# Use new features like multiple dependency groups
[dependency-groups]
dev = ["..."]
test = ["..."]
lint = ["..."]
```

## UV Version Detection

### Checking UV Version:
```bash
$ uv --version
uv 0.9.0

# Check feature support
$ uv help | grep "dependency-groups"
# If found: supports modern format
# If not found: upgrade recommended
```

### CI/CD Configuration:
```yaml
# GitHub Actions
- name: Install UV (latest)
  run: pip install uv

- name: Install UV (specific version)
  run: pip install uv==0.7.5

- name: Check UV version
  run: uv --version
```

## Mend Integration Notes

**Scanning Multi-Version UV Projects:**

Mend should:
- Use latest UV version for scanning
- Resolve all workspace members
- Handle legacy format correctly
- Report dependencies from all services
- No special configuration needed

**Configuration:**
```properties
python.path=/usr/bin/python3.10
python.resolveDependencies=true
python.resolveHierarchyTree=true
```

## Compatibility Matrix

| UV Version | Legacy Format | Dependency Groups | Multiple Groups | Workspace | Status |
|------------|--------------|-------------------|-----------------|-----------|---------|
| 0.4.x      | ✅ Yes       | ❌ No             | ❌ No           | ⚠️  Basic | EOL |
| 0.7.x      | ⚠️  Deprecated | ✅ Yes           | ⚠️  Limited     | ✅ Yes    | Supported |
| 0.9.x+     | ⚠️  Deprecated | ✅ Yes           | ✅ Yes          | ✅ Advanced | Latest |

## Related Tests
- T-P4-010: Python Version Not Installed
- T-P4-011: Very Old Python Version
- T-P4-012: Latest Python Version
- T-P1-005: Lock File Format Compatibility

## Documentation Links
- UV Changelog: https://github.com/astral-sh/uv/releases
- PEP 735 (Dependency Groups): https://peps.python.org/pep-0735/
- UV Workspaces: https://docs.astral.sh/uv/concepts/workspaces/
- UV Migration Guide: https://docs.astral.sh/uv/guides/migration/