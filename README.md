# TieRod DMS

A high-performance, open-source, and self-hosted Dealership Management System (DMS) built specifically for the automotive, heavy machinery, and rental industries. TieRod DMS is designed to replace clunky legacy systems with a modern, microservice-driven, and lightning-fast storefront and parts counter experience.

## Project Goals

### Primary Goal 1: Uncompromising Performance & Isolation
- **Sub-Millisecond Read Times**: Leverage localized memory caching (Redis) alongside high-performance database indexing (Postgres) to ensure parts lookups and inventory checks happen instantly (<1ms).
- **Hard Multi-Tenant Isolation**: Utilize an isolated container-per-store topology. If a single store instance experiences an outage, it remains completely contained, keeping all other branch operations 100% unaffected.

### Primary Goal 2: Modular Enterprise Architecture
- **Microservice/Micro-Frontend Registries**: The central Orchestrator dashboard acts as a modular gateway. New operational tools can be toggled on or deployed as independent services without bloating the core app.
- **Secure-by-Default Infrastructure**: Engineered to run entirely enclosed within an internal enterprise network (Azure VPN / VNet Peering), eliminating external public network exposure while strictly enforcing revocable API tokens and encrypted-at-rest data layers.

## Technical Architecture & Strategy

TieRod DMS utilizes a Hub-and-Spoke topology inside the cloud ecosystem to balance speed with total isolation:

- **The Hub (Orchestrator)**: Powered by Laravel, React, and GraphQL. It manages global telemetry, user permissions, and central reporting metrics. It maintains a master multi-store inventory cache.
- **The Spokes (Child Store Fronts)**: Completely autonomous Docker containers running local instances of Laravel, Postgres, and Redis. They handle daily counter traffic instantly out of memory.
- **The Data Loop**: Child stores run entirely off localized caches to keep network traffic minimal. Every hour, the central Orchestrator uses highly optimized GraphQL queries to pull inventory delta changes and merge them into a master Redis cache, enabling rapid cross-store lookups (3-5ms) across the private internal network backbone without hitting individual store databases.

## Installation
TODO: Add installation guide for Docker Compose and Azure Container Apps.

## Usage
TODO: Add usage guide for localized store provisioning and parts counter lookups.

## Contributing
If you are interested in contributing, thank you. Before your first contribution to TieRod DMS, please familiarise yourself with the guide on [contributing](./CONTRIBUTING.md). This ensures TieRod DMS stays neat and organized for future developers.

## Credits
### Inspiration
- [CDK Global](https://www.cdkglobal.com/) for inspiration, pain points, & first-hand experience of a legacy DMS
- [HBS Systems](https://hbssystems.com/) for the inspiration, pain points, & first-hand experience of a legacy DMS

## License
[LICENSE](./LICENSE)
