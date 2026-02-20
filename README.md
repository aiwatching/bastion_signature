# Bastion Signature

DLP detection patterns for [Bastion AI Gateway](https://github.com/aiwatching/bastion).

## Branch Strategy

Each Bastion version has a corresponding branch:

| Branch | Bastion Version | Status |
|--------|----------------|--------|
| `v0.1.0` | 0.1.0 | Current |

When Bastion upgrades, a new branch is created. Old branches remain available for older installations.

## Directory Structure

```
patterns/
  schema.yaml           # Pattern schema documentation
  high-confidence.yaml  # Low false-positive patterns (API keys, tokens, private keys)
  validated.yaml        # Patterns with structural validators (credit card, SSN)
  context-aware.yaml    # Patterns that require context keywords nearby
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
2. All `patterns/*.yaml` files are parsed
3. Patterns are merged into the SQLite database (upsert by name)
4. Remote patterns can be toggled on/off from the Dashboard, same as built-in patterns

### Adding patterns

1. Fork this repo
2. Add patterns to an existing YAML file or create a new one under `patterns/`
3. Follow the schema in `patterns/schema.yaml`
4. Submit a PR

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
