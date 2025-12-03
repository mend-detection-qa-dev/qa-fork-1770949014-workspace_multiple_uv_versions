# Service B: Modern UV 0.7.x Features

## UV Version Compatibility
**Target UV Version**: 0.7.x+
**Status**: Modern format with PEP 735 dependency groups

## Configuration Format

### Modern Dependency Groups
This service uses the **modern** `[dependency-groups]` format:

```toml
[dependency-groups]
dev = [
    "pytest>=8.0.0",
    "pytest-asyncio>=0.23.0",
    "pytest-cov>=4.1.0",
]
```

### Workspace Dependencies
This service depends on `service-a-legacy` from the same workspace:

```toml
dependencies = [
    "service-a-legacy",  # Internal workspace dependency
    # ... other dependencies
]

[tool.uv.sources]
service-a-legacy = { workspace = true }
```

## UV 0.7.x Features Used

### 1. PEP 735 Dependency Groups
```toml
[dependency-groups]
dev = ["pytest>=8.0.0"]
```

### 2. Workspace Sources
```toml
[tool.uv.sources]
service-a-legacy = { workspace = true }
```

### 3. Workspace Member Detection
- UV automatically detects workspace members
- Resolves internal dependencies correctly
- Generates unified lock file

## Expected Behavior

With UV 0.7.x or later:
```bash
$ uv sync
Resolved 65 packages in 1.0s
Installed 65 packages in 2.2s

$ uv run python -c "import service_a_legacy"
# Success - can import workspace dependency
```

## Service Purpose
- Demonstrates modern UV configuration
- Uses PEP 735 dependency groups
- Shows workspace internal dependencies
- FastAPI service with Redis caching
- Depends on service-a for shared logic

## Differences from Service A

| Feature | Service A | Service B |
|---------|-----------|-----------|
| Dev Dependencies | `[tool.uv]` | `[dependency-groups]` |
| UV Version | 0.4.x+ | 0.7.x+ |
| Workspace Deps | None | service-a-legacy |
| Format | Legacy | Modern |
| Status | Deprecated | Recommended |