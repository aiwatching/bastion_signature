# Bastion Signature

DLP detection patterns for [Bastion AI Gateway](https://github.com/aiwatching/bastion).

## Versioning

There are **two independent version numbers**:

| Version | Meaning | Example |
|---------|---------|---------|
| **Git branch** | Bastion compatibility version | `v0.1.0` = works with Bastion 0.1.0 |
| **Signature version** (`signature.yaml → version`) | Pattern revision number, incremented on every update | `1`, `2`, `3`... |

The git branch stays the same until Bastion upgrades. The signature version increments every time patterns are added, modified, or removed within that branch.

### Current status

| Branch | Bastion Version | Signature Version | Patterns |
|--------|----------------|-------------------|----------|
| `v0.1.0` | 0.1.0 | 1 | 27 |

## Directory Structure

```
signature.yaml            # Manifest: signature version, changelog
patterns/
  schema.yaml             # Pattern format documentation
  high-confidence.yaml    # Low false-positive patterns (API keys, tokens, private keys)
  validated.yaml          # Patterns with structural validators (credit card, SSN)
  context-aware.yaml      # Patterns that require context keywords nearby
```

## Usage

### In Bastion config (`~/.bastion/config.yaml`)

```yaml
plugins:
  dlp:
    remotePatterns:
      url: "https://github.com/aiwatching/bastion_signature.git"
      branch: "auto"          # "auto" = match Bastion VERSION, or specify e.g. "v0.1.0"
      syncOnStart: true       # Pull latest on startup
      syncIntervalMinutes: 0  # 0 = startup only, >0 = periodic sync
```

### How it works

1. On startup (if `syncOnStart: true`), Bastion clones/pulls this repo to `~/.bastion/signatures/`
2. Reads `signature.yaml` for version info
3. All `patterns/*.yaml` files are parsed
4. Patterns are merged into the SQLite database (upsert by name)
5. Dashboard shows current signature version and notifies when updates are available

### Adding patterns

1. Fork this repo
2. Add patterns to an existing YAML file or create a new one under `patterns/`
3. Follow the schema in `patterns/schema.yaml`
4. **Increment `version` in `signature.yaml`** and update `patternCount`
5. Submit a PR

## Pattern Schema

See [patterns/schema.yaml](patterns/schema.yaml) for the full specification.

Quick example:

```yaml
patterns:
  - name: my-service-api-key
    category: high-confidence
    regex: 'msvc_[A-Za-z0-9]{40}'
    flags: g
    description: My Service API Key
```

## License

Apache-2.0
