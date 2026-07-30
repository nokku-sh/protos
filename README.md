# Protobuf Schemas

This repository contains Protocol Buffer schema definitions for Nokku APIs. These schemas are used to generate client libraries and server stubs across multiple programming languages.

## Overview

- **Organization**: `nokku-sh`
- **Structure**: Each project has its own directory with versioned schemas
- **Tooling**: Managed with [Buf](https://buf.build/) for schema validation and code generation

## Usage with Buf

This repository uses [Buf](https://buf.build/) for schema management and validation. To consume these APIs in your project:

### As a Buf Dependency

Add to your `buf.yaml`:

```yaml
deps:
  - buf.build/nokku-sh/protos
```

Or use the BSR (Buf Schema Registry):

```bash
buf mod update
```

### Generate Code

Generate code for your preferred language:

```bash
# Generate for Go
buf generate --template buf.gen.yaml

# Generate for TypeScript
buf generate --template buf.gen.ts.yaml

# Generate for Python
buf generate --template buf.gen.py.yaml
```

### Local Development

To work with these schemas locally:

```bash
# Clone the repository
git clone <repository-url>
cd protos

# Install Buf (if not already installed)
# https://docs.buf.build/installation

# Lint the schemas
buf lint

# Check for breaking changes
buf breaking --against '.git#branch=main'

# Generate code
buf generate
```

## Schema Validation

All schemas are validated using:

- **Standard linting rules** via `STANDARD` configuration
- **Breaking change detection** via `FILE` configuration
- **ProtoValidate** for runtime validation

## Dependencies

This repository depends on:

- `googleapis/googleapis` - Google API common types
- `bufbuild/protovalidate` - ProtoValidate extensions

## Contributing

When making changes to protobuf schemas:

1. Always run `buf lint` to ensure schema quality
2. Use `buf breaking` to check for breaking changes
3. Update documentation for any API changes
4. Consider backward compatibility for all modifications
