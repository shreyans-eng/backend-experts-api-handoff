# Changelog

All notable **published API image** versions for the frontend handoff.

Format: image tag → what FE should expect. Versions are immutable — never reuse a tag for different builds.

## [v1.0.0] — 2026-08-24

### Added

- Initial handoff release of the Coinzy Experts API Docker image.
- Compose stack: API (`:3000`), MongoDB 7, Redis 7.
- Health at `/health`, API docs at `/docs`.

### Image

```text
ghcr.io/trackzio-mobile-apps/coinzy-experts-api:v1.0.0
```
