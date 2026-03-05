# oss-workflows

Reusable GitHub Actions workflows for open-source projects in the [nandlabs](https://github.com/nandlabs) organization.

## Structure

Workflows are organized by language prefix under `.github/workflows/`:

```
oss-workflows/
├── .github/
│   └── workflows/
│       ├── go-ci.yml
│       ├── go-lint.yml
│       ├── go-codeql.yml
│       ├── go-dependency-review.yml
│       ├── go-release.yml
│       └── go-stale.yml
└── README.md
```

## Go Workflows

| Workflow              | File                       | Description                                                   |
| --------------------- | -------------------------- | ------------------------------------------------------------- |
| **CI**                | `go-ci.yml`                | Build & test across multiple Go versions with coverage upload |
| **Lint**              | `go-lint.yml`              | golangci-lint, go vet, and staticcheck                        |
| **CodeQL**            | `go-codeql.yml`            | CodeQL security analysis for Go                               |
| **Dependency Review** | `go-dependency-review.yml` | Dependency vulnerability and license scanning                 |
| **Release**           | `go-release.yml`           | Automated GitHub release with changelog on tag push           |
| **Stale**             | `go-stale.yml`             | Auto-close stale issues and PRs                               |

## Usage

Reference these workflows from any repository using `uses`:

### CI

```yaml
name: "CI"

on:
  pull_request:
    branches: ["*"]
  push:
    branches: ["main"]

jobs:
  ci:
    uses: nandlabs/oss-workflows/.github/workflows/go-ci.yml@main
    with:
      go-versions: '["1.25", "1.26"]'
      coverage-go-version: "1.25"
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `go-versions` | string | `'["1.25", "1.26"]'` | JSON array of Go versions |
| `coverage-go-version` | string | `"1.25"` | Go version for coverage upload |

### Lint

```yaml
name: "Lint"

on:
  pull_request:
    branches: ["*"]
  push:
    branches: ["main"]

jobs:
  lint:
    uses: nandlabs/oss-workflows/.github/workflows/go-lint.yml@main
    with:
      go-version: "1.25"
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `go-version` | string | `"1.25"` | Go version for linting |

### CodeQL

```yaml
name: "CodeQL Analysis"

on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]
  schedule:
    - cron: "0 0 * * 1"

jobs:
  codeql:
    uses: nandlabs/oss-workflows/.github/workflows/go-codeql.yml@main
    permissions:
      actions: read
      contents: read
      security-events: write
    with:
      go-version: "1.25"
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `go-version` | string | `"1.25"` | Go version for CodeQL |

### Dependency Review

```yaml
name: "Dependency Review"

on:
  pull_request:
    branches: ["main"]

jobs:
  dependency-review:
    uses: nandlabs/oss-workflows/.github/workflows/go-dependency-review.yml@main
    permissions:
      contents: read
      pull-requests: write
    with:
      fail-on-severity: "moderate"
      allow-licenses: "MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC"
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `fail-on-severity` | string | `"moderate"` | Minimum severity to fail |
| `allow-licenses` | string | `"MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC"` | Allowed licenses |

### Release

```yaml
name: "Release"

on:
  push:
    tags: ["v*.*.*"]

jobs:
  release:
    uses: nandlabs/oss-workflows/.github/workflows/go-release.yml@main
    permissions:
      contents: write
    with:
      go-version: "1.25"
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `go-version` | string | `"1.25"` | Go version for release verification |

**Secrets:**
| Secret | Required | Description |
|--------|----------|-------------|
| `token` | Yes | GitHub token for creating releases |

### Stale

```yaml
name: "Stale Issues"

on:
  schedule:
    - cron: "0 1 * * *"
  workflow_dispatch:

jobs:
  stale:
    uses: nandlabs/oss-workflows/.github/workflows/go-stale.yml@main
    permissions:
      issues: write
      pull-requests: write
```

**Inputs:**
| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `days-before-stale` | number | `60` | Days before marking stale |
| `days-before-close` | number | `14` | Days before closing stale |
| `exempt-issue-labels` | string | `"pinned,security,bug"` | Exempt issue labels |
| `exempt-pr-labels` | string | `"pinned,security"` | Exempt PR labels |

## Adding New Ecosystems

To add workflows for a new language or ecosystem (e.g., Python, Node.js), create a new directory:

To add workflows for a new language or ecosystem (e.g., Python, Node.js), use a language prefix:

```
.github/workflows/
├── go-*.yml       # Go workflows
├── python-*.yml   # Python workflows (future)
└── node-*.yml     # Node.js workflows (future)
```

## Consumers

- [golly](https://github.com/nandlabs/golly)
- [golly-aws](https://github.com/nandlabs/golly-aws)

## License

MIT
