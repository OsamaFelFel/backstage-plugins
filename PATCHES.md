# idp-lab patches

This branch is upstream tag `v1.3.1` of
[openchoreo/backstage-plugins](https://github.com/openchoreo/backstage-plugins)
plus our changes, one commit per change. `main` is upstream `main`, unchanged.
For a new OpenChoreo release, create `idp/<new tag>` from that tag, replay these
commits in order, fix conflicts and rerun the portal tests. Jenkins builds the
portal image from this branch and pushes `images/openchoreo-portal:<commit>` to Nexus.

Everything else of ours is a new file: `Jenkinsfile` and this `PATCHES.md`.

## 1. Published GitLab, HTTP request and Vault modules (T6.01)

Why: the stock portal bundles only GitHub publishing. Our templates open GitLab
merge requests (`@backstage/plugin-scaffolder-backend-module-gitlab` 0.12.0) and
start Jenkins jobs through Backstage's proxy
(`@roadiehq/scaffolder-backend-module-http-request` 5.9.0), and the Vault plugin
(frontend 0.21.0, backend 0.23.0) lists each component's secrets (D5). Plugins are
compiled into the image, so they must be registered in the source. The upstream
Jenkins frontend (0.28.0) is already registered and unchanged.

Upstream files changed:

- `packages/portal-app/package.json`: add the Vault frontend dependency.
- `packages/portal-app/src/createPortalApp.tsx`: register the Vault frontend.
- `packages/portal-backend/package.json`: add the GitLab, HTTP request and Vault backend dependencies.
- `packages/portal-backend/src/features.ts`: register the three backend modules.
- `yarn.lock`: lock the added packages.

New file: `Jenkinsfile` builds the unmodified upstream Dockerfile with the pinned
Node22 build and runtime images and pushes the image to Nexus.

## 2. Templates open to any signed-in user (T6.05)

Why: the stock policy lets a user run a template only with OpenChoreo API create
rights. Our templates only open GitLab merge requests, so Git-only developers must
be able to run them. Only scaffolder task submission changes: a signed-in user
(`userEntityRef` present) may submit, anonymous submission is denied. Every
OpenChoreo resource action keeps its own check; no API create right is granted.

Upstream file changed:

- `plugins/permission-backend-module-openchoreo-policy/src/policy/OpenChoreoPermissionPolicy.ts`: `handleScaffolderTaskCreate`.
