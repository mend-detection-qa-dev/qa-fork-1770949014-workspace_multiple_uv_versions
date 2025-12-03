# Service C: Latest UV 0.9.x+ Features

## UV Version Compatibility
**Target UV Version**: 0.9.x+
**Status**: Latest features, cutting-edge configuration

## Configuration Format

### Multiple Dependency Groups
This service uses **multiple** dependency groups (UV 0.9.x+ / PEP 735 full support):

```toml
[dependency-groups]
dev = ["pytest>=8.3.0", "pytest-asyncio>=0.24.0"]
test = ["pytest-cov>=6.0.0", "coverage>=7.6.0"]
lint = ["ruff>=0.7.0", "mypy>=1.13.0"]
docs = ["mkdocs>=1.6.0", "mkdocs-material>=9.5.0"]
```

### Optional Dependencies
```toml
[project.optional-dependencies]
ml = ["torch>=2.1.0", "transformers>=4.36.0"]
monitoring = ["prometheus-client>=0.20.0"]
```

### Workspace Dependencies
Depends on both service-a and service-b:

```toml
dependencies = [
    "service-a-legacy",
    "service-b-modern",
    # ... other dependencies
]

[tool.uv.sources]
service-a-legacy = { workspace = true }
service-b-modern = { workspace = true }
```

## UV 0.9.x+ Features Used

### 1. Multiple Dependency Groups
```bash
# Install specific groups
$ uv sync --group dev
$ uv sync --group test
$ uv sync --group lint
$ uv sync --group docs

# Install multiple groups
$ uv sync --group dev --group test

# Install all groups
$ uv sync --all-groups
```

### 2. Optional Dependencies
```bash
# Install with ML extras
$ uv sync --extra ml

# Install with monitoring extras
$ uv sync --extra monitoring

# Install with all extras
$ uv sync --all-extras
```

### 3. Advanced Workspace Configuration
- Multiple internal dependencies
- Complex dependency tree
- Unified lock file resolution

## Expected Behavior

With UV 0.9.x or later:
```bash
$ uv sync
Resolved 95 packages in 0.8s
Installed 95 packages in 2.0s

# Install specific groups
$ uv sync --group test
Resolved 102 packages in 0.9s
Installed 7 additional packages

$ uv sync --group lint
Resolved 106 packages in 0.9s
Installed 4 additional packages

# Import workspace dependencies
$ uv run python -c "import service_a_legacy; import service_b_modern"
# Success
```

## Service Purpose
- Demonstrates latest UV features
- Uses multiple dependency groups
- Shows complex workspace dependencies
- Advanced FastAPI service with:
  - Database migrations (Alembic)
  - Data processing (Pandas, Polars)
  - Optional ML features (PyTorch)
  - Monitoring capabilities

## Comparison with Other Services

| Feature | Service A | Service B | Service C |
|---------|-----------|-----------|-----------|
| Dev Dependencies | `[tool.uv]` | `[dependency-groups]` | Multiple groups |
| UV Version | 0.4.x+ | 0.7.x+ | 0.9.x+ |
| Dependency Groups | 1 (legacy) | 1 (dev) | 4 (dev/test/lint/docs) |
| Workspace Deps | None | service-a | service-a, service-b |
| Optional Deps | No | No | Yes (ml, monitoring) |
| Format | Legacy | Modern | Latest |
| Status | Deprecated | Recommended | Cutting-edge |

## Advanced Use Cases

### Development Workflow:
```bash
# Setup development environment
$ uv sync --group dev

# Run tests
$ uv run pytest

# Run linting
$ uv sync --group lint
$ uv run ruff check .
$ uv run mypy src/

# Build documentation
$ uv sync --group docs
$ uv run mkdocs build
```

### Production Deployment:
```bash
# Install only production dependencies
$ uv sync

# With ML features
$ uv sync --extra ml

# With monitoring
$ uv sync --extra monitoring
```

### CI/CD Pipeline:
```yaml
# GitHub Actions example
- name: Install dependencies
  run: uv sync --group test --group lint

- name: Run tests
  run: uv run pytest

- name: Lint
  run: uv run ruff check .
```

## Migration from Service B

To migrate from service-b's single dependency group to service-c's multiple groups:

```toml
# Before (service-b)
[dependency-groups]
dev = [
    "pytest>=8.0.0",
    "pytest-cov>=4.1.0",
    "ruff>=0.3.0",
]

# After (service-c) - Separate concerns
[dependency-groups]
test = ["pytest>=8.3.0", "pytest-cov>=6.0.0"]
lint = ["ruff>=0.7.0"]
```

**Benefits**:
- Install only what you need
- Faster CI/CD (install test deps for testing, lint deps for linting)
- Clearer separation of concerns
- More granular control

## Why Multiple Dependency Groups?

### 1. Performance
```bash
# Old way: Install everything
$ uv sync --group dev  # Installs pytest, ruff, mypy, mkdocs, etc.

# New way: Install only what you need
$ uv sync --group test  # Only pytest and coverage
$ uv sync --group lint  # Only ruff and mypy
```

### 2. CI/CD Optimization
```yaml
# Separate jobs can install only relevant dependencies
test-job:
  - run: uv sync --group test

lint-job:
  - run: uv sync --group lint

docs-job:
  - run: uv sync --group docs
```

### 3. Developer Experience
```bash
# Frontend developer: doesn't need linting tools
$ uv sync --group dev

# Backend developer: needs everything
$ uv sync --all-groups

# Documentation writer: only needs docs tools
$ uv sync --group docs
```