# Service A: Legacy UV 0.4.x Compatible

## UV Version Compatibility
**Target UV Version**: 0.4.x - 0.6.x
**Status**: Legacy format (still works but deprecated)

## Configuration Format

### Legacy Dev Dependencies
This service uses the **deprecated** `[tool.uv]` dev-dependencies format:

```toml
[tool.uv]
dev-dependencies = [
    "pytest>=7.4.0,<8.0.0",
    "pytest-asyncio>=0.21.0",
]
```

### Why Legacy Format?
- Original UV format from early 2024
- Pre-PEP 735 dependency groups
- Still functional in modern UV (with warnings)
- Common in older projects not yet migrated

## Expected Warnings with Modern UV

When using UV 0.7.x or later:
```bash
$ uv lock
⚠️  Warning: The `tool.uv.dev-dependencies` field is deprecated
    and will be removed in a future release. Use `dependency-groups` instead.

Resolved 45 packages in 1.2s
```

## Migration to Modern Format

To migrate this service to modern format:

```toml
# Remove:
[tool.uv]
dev-dependencies = ["pytest>=7.4.0"]

# Add:
[dependency-groups]
dev = ["pytest>=8.0.0"]
```

## Service Purpose
- Demonstrates legacy UV configuration
- Tests backwards compatibility
- Shows migration path
- Basic FastAPI service with SQLAlchemy