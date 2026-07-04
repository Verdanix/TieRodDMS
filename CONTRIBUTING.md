# Contributing to TieRodDMS: Guidelines for Developers, Tinkerers, and End Users

Welcome to the TieRodDMS open-source community! We are excited to have you on board as we work together to build a blazing-fast, decentralized, and deeply isolated open-source Dealership Management System (DMS) built specifically for the automotive, machinery, and rental equipment industries. Our mission is to crush slow, legacy, multi-menu corporate giants by providing parts counters with sub-millisecond local caching, modern visual flows, and seamless, self-hosted multi-tenant operations.

## Getting Started
Before diving into the contributing guidelines, please take a moment to familiarize yourself with the project:
- Visit the [README.md](./README.md) for an overview of TieRodDMS features and capabilities.
- Review the [LICENSE](./LICENSE) to understand our open-source terms of use.
- Review the [CODE OF CONDUCT](./CODE_OF_CONDUCT.md) to understand how to keep our community welcoming and collaborative.

## Code Style Guidelines
To ensure consistency and maintainability across both our central orchestrator and isolated child store storefronts, please adhere to these coding standards:

- **Type Safety First**: We use **TypeScript** for all frontend. Every API payload, component prop, and state tree must be explicitly typed.
- **Backend Standards**: Our core service layers leverage Laravel. Follow strict PSR-12 coding standards, utilize Form Requests for API input validation, and stick to strict Type Hinting on all method signatures.
- **Style Guide**: We enforce standard ESLint and Prettier rules for the React/TS frontend to keep code clean and readable. Run `npm run lint` before committing.
- **Documentation**: Use comments to document complex business logic, background workers, and caching models. Code comments should focus heavily on **why** certain technical choices were made (such as network optimizations or race-condition defenses) rather than just *what* the code does.

## Tech Stack
TieRodDMS is split into two primary architectures: the **Central Orchestrator** and individual, multi-tenant **Child Store Applications** deployed via Docker.

*   **Frontend**: React, TypeScript, Tailwind CSS
*   **Backend Framework**: Laravel (PHP)
*   **API Layer**: GraphQL (enabling lean data requests for maximum performance)
*   **State & Caching Layer**: Redis (In-memory reads for sub-millisecond lookup speeds)
*   **Database Persistent Layer**: PostgreSQL (Hard-isolated databases per store instance)
*   **Infrastructure**: Docker Containers for simple & easy deployment

## Caching & Performance Architecture
Contributions impacting the core inventory layer must strictly respect our performance blueprint:
- **Local Sub-MS Speed**: All local storefront part lookups must check the store's local Redis container *first*, falling back to local Postgres only on a cache miss.
- **Asynchronous Global Merging**: Do not build direct store-to-store live network hooks. To prevent network congestion and store cross-dependencies, the central Orchestrator periodically polls child stores hourly for compressed inventory delta changes, merging them into a central global Redis cache. Cross-store lookups must be executed via quick GraphQL queries against this central aggregated cache.

## Testing
To ensure our critical store operations remain reliable, secure, and crash-proof, we implement a multi-layered testing workflow. All pull requests require testing coverage:

- **Automated Testing**: Built seamlessly into our CI/CD workflow utilizing PHPUnit for backend endpoints and Vitest for the React UI.
- **Unit Testing**: Conducted for individual TypeScript helper functions, Laravel service classes, and data transformers to validate standalone logic.
- **Integration Testing**: Explicitly required for our GraphQL schemas and Redis caching layers to ensure state consistency during reads and writes.
- **System & Race-Condition Testing**: Focused on database locking, API token invalidations, and high-frequency concurrent checkout simulation at the parts counter.

## Development Phase

### Build and Deployment
- **Deployment Strategy**: A full-release rollout architecture is employed, where new features and updates are deployed in unified versions across the multi-tenant store structures. This ensures structural parity between the global orchestrator panel and individual child container images.
- **Target Environments**:
    *   **Development**: The primary localized workspace for developers running local Docker Compose environments.
    *   **Testing**: An automated, isolated environment matching production configurations where code changes are analyzed instantly post-commit.
    *   **Production**: The secure, final production instance ready for each store's IT team to deploy.

### Automated CI/CD Pipeline
- **PRs**: All feature contributions and bug fixes must target the `development` branch.
- **Testing Branch**: Once a release tag is prepared, code shifts to the `testing` branch where integrated system pipelines validate network resilience and schema updates.
- **Deployment**: Upon passing staging checks, a formal release is merged into `production`, safely rebuilding and rolling over the corresponding Docker images.

## Infrastructure and Monitoring
- **Identical Staging**: Testing and development container environments should replicate production resource constraints to surface potential race conditions or performance drops early.
- **Fail-Safe Backups**: Our update and deployment scripts auto-generate compressed zip/tarball schema snapshots and transactional safety states to allow fast, manual rollbacks if a container update fails.
- **Self-Healing Design**: Microservices and store sub-apps must be written with graceful exits, health-check endpoints, and self-healing worker loops so an infrastructure hiccup never stalls an active parts counter.

## Contributing Guidelines
1. Fork the TieRodDMS repository and create your feature branch (`git checkout -b feature/amazing-feature`).
2. Implement your changes locally, ensuring you write comprehensive unit tests.
3. Keep your commit messages clear, concise, and structured (e.g., `feat(inventory): add automatic local Redis cache invalidation`).
4. Push your branch to your remote fork.
5. Submit a detailed Pull Request against our `development` branch.
6. Engage with our core reviewers during architectural code reviews and discussions.
7. Ensure all automated CI pipelines pass successfully before requesting final approval.

By adhering to these architectural guidelines, we preserve a fast, heavily protected, and incredibly scalable platform that the entire automotive and heavy equipment industry can rely on. Thank you for making TieRodDMS better!
