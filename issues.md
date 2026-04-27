# Vatix Backend 100 Individual Issues

## Create `src/api` folder
Description
Create the API module root folder and initial README so all HTTP-facing code has a clear home.
Directory: apps/api
Background
Without a predictable structure, endpoint code spreads across the repo and slows onboarding.
Acceptance criteria
- `apps/api` exists in the repository
- `apps/api/README.md` explains scope and ownership
- Existing backend startup still works after folder creation
Notes
- Keep this as scaffolding only; no business logic yet

## Create `src/indexer` folder
Description
Create the indexer module root folder for ledger/event ingestion responsibilities.
Directory: apps/indexer
Background
The indexer is a core backend service and should be isolated from request/response API code.
Acceptance criteria
- `apps/indexer` exists
- `apps/indexer/README.md` describes ingestion responsibilities
- Folder is referenced in top-level project map
Notes
- This issue is strictly structural

## Create `src/oracle` folder
Description
Create the oracle module root for resolution-provider adapters and workflows.
Directory: apps/oracle
Background
Oracle logic has unique reliability and trust constraints and should be separated early.
Acceptance criteria
- `apps/oracle` exists
- `apps/oracle/README.md` describes boundaries
- No cross-import from API internals
Notes
- Keep interfaces minimal in this step

## Create `src/shared` folder
Description
Create shared utilities module for config, logger, error types, and common contracts.
Directory: packages/shared
Background
Shared utilities reduce duplication and establish consistency across API, indexer, and oracle.
Acceptance criteria
- `packages/shared` exists
- Shared README defines what can and cannot live here
- At least one common helper module is stubbed
Notes
- Avoid placing feature/business logic in shared

## Create `src/workers` folder
Description
Create a worker module folder for queue consumers and scheduled jobs.
Directory: apps/workers
Background
Background execution paths differ from HTTP request lifecycles and need independent structure.
Acceptance criteria
- `apps/workers` exists
- Worker README defines queue and scheduler scope
- Folder is included in architecture docs
Notes
- No queues required yet; only module setup

## Add root `README.md` project map section
Description
Add a concise module map in root README describing API, indexer, oracle, workers, and shared.
Directory: .
Background
Contributors need a fast orientation to avoid adding code in the wrong place.
Acceptance criteria
- Root README includes a "Project map" section
- Each module has one-line purpose text
- README links to module-level READMEs
Notes
- Keep this section short and contributor-focused

## Add `docs/architecture.md` starter file
Description
Create an architecture starter doc capturing system boundaries and major data flow.
Directory: docs
Background
Early architecture docs reduce accidental coupling in a fast-moving open-source project.
Acceptance criteria
- `docs/architecture.md` exists
- Contains service boundary diagram/description
- Lists open decisions and assumptions
Notes
- A draft is acceptable if clearly labeled

## Add `scripts/` folder for utility commands
Description
Create scripts directory for dev utilities like bootstrap, seed, and maintenance tasks.
Directory: scripts
Background
Ad-hoc commands in chat or shell history are hard to share and reproduce.
Acceptance criteria
- `scripts/` exists
- Includes README describing script conventions
- Execution permissions documented
Notes
- Prefer shell-agnostic scripts where possible

## Add `tmp/` to `.gitignore`
Description
Ignore temporary files generated during local development and debugging.
Directory: .
Background
Temporary files create noisy diffs and accidental commits.
Acceptance criteria
- `.gitignore` contains `tmp/`
- `git status` no longer tracks files under `tmp/`
- Existing tracked files are unaffected
Notes
- Keep ignore rule scoped to repo root tmp folder

## Add `notes/` to `.gitignore`
Description
Ignore private notes folder so personal planning docs are not committed.
Directory: .
Background
Maintainers often keep local notes; those should remain untracked by default.
Acceptance criteria
- `.gitignore` contains `notes/`
- Files under `notes/` stay untracked
- Rule does not ignore similarly named production folders
Notes
- This is a local workflow safeguard

## Create `.env.example` baseline
Description
Add a canonical `.env.example` listing required environment variables for all services.
Directory: .
Background
Missing env docs is a common source of failed local setup and CI drift.
Acceptance criteria
- `.env.example` exists
- All required vars are listed with placeholders
- Optional vars are clearly marked
Notes
- Do not include real credentials

## Add `NODE_ENV` variable
Description
Document and consume `NODE_ENV` across all service entrypoints.
Directory: packages/shared
Background
Runtime behavior (logging, stack traces, caching) often depends on environment mode.
Acceptance criteria
- `NODE_ENV` listed in `.env.example`
- Config loader validates accepted values
- Service logs include current environment
Notes
- Accepted values should be constrained (dev/test/prod)

## Add `PORT` variable
Description
Use configurable `PORT` for API service binding.
Directory: apps/api
Background
Hardcoded ports conflict in local dev and managed deployments.
Acceptance criteria
- `PORT` listed in `.env.example`
- API starts on configured port
- Invalid port values fail validation
Notes
- Include default fallback for local dev

## Add `DATABASE_URL` variable
Description
Define and validate `DATABASE_URL` for persistence access.
Directory: packages/shared
Background
Database connection configuration should be centralized and validated at startup.
Acceptance criteria
- `DATABASE_URL` listed in `.env.example`
- Startup fails clearly when missing
- URL format is validated before connecting
Notes
- Do not log full connection strings

## Add `STELLAR_RPC_URL` variable
Description
Add and validate Stellar RPC endpoint variable for chain reads.
Directory: apps/indexer
Background
Indexer and oracle services depend on chain/RPC connectivity to function.
Acceptance criteria
- `STELLAR_RPC_URL` documented in `.env.example`
- Config parser validates URL shape
- Health/readiness includes RPC reachability status
Notes
- Support per-environment RPC endpoints

## Add `SOROBAN_NETWORK_PASSPHRASE` variable
Description
Capture network passphrase setting for Soroban transactions and verification.
Directory: apps/indexer
Background
Wrong network selection can produce invalid reads/writes and misleading data.
Acceptance criteria
- Variable exists in `.env.example`
- Config uses passphrase in chain client setup
- Startup warns on known invalid values
Notes
- Add comments for testnet/mainnet examples

## Add `LOG_LEVEL` variable
Description
Introduce centralized log-level control for API, indexer, and workers.
Directory: packages/shared
Background
Consistent observability needs runtime-adjustable verbosity.
Acceptance criteria
- `LOG_LEVEL` documented in `.env.example`
- Logger reads value from config
- Invalid levels fallback safely with warning
Notes
- Use a shared level enum across services

## Add `ORACLE_POLL_INTERVAL_MS` variable
Description
Add configurable polling interval for oracle ingestion and resolution checks.
Directory: apps/oracle
Background
Polling intervals need tuning across environments and incident response.
Acceptance criteria
- Variable present in `.env.example`
- Oracle scheduler reads this value
- Lower/upper safety bounds enforced
Notes
- Document recommended default

## Create `src/shared/config.ts`
Description
Implement a single typed config loader that all services consume.
Directory: packages/shared/src
Background
Scattered config parsing causes inconsistent behavior and hidden runtime bugs.
Acceptance criteria
- `config.ts` exports typed config object
- Includes validation and helpful errors
- API/indexer/oracle consume shared config module
Notes
- Keep this module side-effect free

## Fail startup when required env is missing
Description
Add fail-fast boot checks for mandatory runtime configuration.
Directory: packages/shared/src
Background
Silent defaults can let services run in broken states with stale/empty data.
Acceptance criteria
- Missing required env exits process with non-zero code
- Error lists exactly which keys are missing
- Behavior covered by unit test
Notes
- Prefer startup validation over lazy runtime failures

## Add `/v1` API prefix
Description
Version all public API routes under `/v1`.
Directory: apps/api
Background
Versioning is required for stable external integrations and future evolution.
Acceptance criteria
- Existing routes moved under `/v1`
- Non-versioned routes return 404 or redirect policy
- OpenAPI/docs reflect versioned paths
Notes
- Maintain backwards compatibility policy in docs

## Add `GET /v1/health` endpoint
Description
Create lightweight liveness endpoint for process and dependency baseline checks.
Directory: apps/api
Background
Orchestrators and uptime checks need fast liveness signals.
Acceptance criteria
- Endpoint returns HTTP 200 when process is running
- Response includes basic service metadata
- No expensive downstream calls performed
Notes
- Keep response stable for monitoring tools

## Add `GET /v1/ready` endpoint
Description
Create readiness endpoint validating required downstream dependencies.
Directory: apps/api
Background
A live process may still be unable to serve valid data if dependencies fail.
Acceptance criteria
- Endpoint checks DB and index freshness dependency
- Returns non-200 when critical dependency fails
- Response body lists dependency statuses
Notes
- Separate liveness and readiness semantics clearly

## Add standard API success shape
Description
Define a single success response envelope for all endpoints.
Directory: apps/api
Background
Consistent response contracts simplify frontend and SDK integration.
Acceptance criteria
- Shared success DTO/interface added
- At least three endpoints use new shape
- API docs include success envelope schema
Notes
- Include `requestId` in envelope for traceability

## Add standard API error shape
Description
Create standardized error envelope with code, message, and metadata.
Directory: apps/api
Background
Clients need reliable error handling logic, not endpoint-specific formats.
Acceptance criteria
- Error middleware uses common schema
- Includes stable `code` field
- Hides internal stack traces in production
Notes
- Keep machine-readable and human-readable fields

## Add global 404 handler
Description
Add a centralized handler for unknown routes.
Directory: apps/api
Background
Default framework 404 responses are often inconsistent with API contract.
Acceptance criteria
- Unknown route returns standardized error envelope
- HTTP status is 404
- Includes request path and request ID
Notes
- Ensure this runs after all route registrations

## Add global error middleware
Description
Implement one global error layer to normalize and log failures.
Directory: apps/api
Background
Scattered try/catch handling leads to inconsistent responses and missed logs.
Acceptance criteria
- Unhandled route errors reach middleware
- Errors mapped to consistent status and code
- Logs include stack trace in non-production only
Notes
- Include support for validation errors separately

## Add request ID middleware
Description
Attach or propagate request IDs for tracing across services.
Directory: apps/api
Background
Troubleshooting distributed flows requires correlation identifiers.
Acceptance criteria
- Incoming `x-request-id` accepted and validated
- New ID generated when absent
- Request ID returned in response headers
Notes
- Use UUID format for generated IDs

## Add basic CORS config
Description
Configure explicit CORS policy for allowed origins and methods.
Directory: apps/api
Background
Overly permissive CORS is a security risk; overly strict blocks clients.
Acceptance criteria
- Allowed origins configurable via env
- Preflight requests succeed for allowed origins
- Disallowed origins are rejected
Notes
- Start with restrictive defaults

## Add API request logging middleware
Description
Log request method, path, status, duration, and request ID.
Directory: apps/api
Background
Access logs are foundational for debugging and performance monitoring.
Acceptance criteria
- Logs emitted once per request
- Excludes sensitive headers/body content
- Includes latency in milliseconds
Notes
- Keep log format machine-parseable

## Add `GET /v1/markets` endpoint
Description
Implement market listing endpoint returning active and historical markets.
Directory: apps/api
Background
Market discovery is a core user flow for the frontend.
Acceptance criteria
- Endpoint returns paginated market list
- Default sort is deterministic
- Response uses standard success schema
Notes
- Start with read-only data from projections

## Add `status` filter for markets list
Description
Allow filtering markets by lifecycle status (open, closed, resolved).
Directory: apps/api
Background
Clients need targeted views for UX and analytics.
Acceptance criteria
- `status` query param accepted
- Invalid status returns validation error
- Filter is covered by integration tests
Notes
- Document accepted status values

## Add `category` filter for markets list
Description
Support category-based filtering in market discovery endpoint.
Directory: apps/api
Background
Category filtering improves discoverability and browsing performance.
Acceptance criteria
- `category` query param supported
- Case handling documented and consistent
- Combined filters (status + category) work
Notes
- Consider normalized category taxonomy later

## Add pagination `page` param
Description
Add `page` query parameter parsing and validation to list endpoints.
Directory: apps/api
Background
Stable pagination is required for scalable data retrieval.
Acceptance criteria
- `page` defaults to 1
- Values below 1 are rejected
- Pagination metadata included in response
Notes
- Keep behavior consistent across list endpoints

## Add pagination `limit` param
Description
Add `limit` query parameter with safe bounds.
Directory: apps/api
Background
Unbounded result sets create latency and resource pressure.
Acceptance criteria
- `limit` has default and max cap
- Invalid values trigger validation error
- Query uses limit safely in datastore layer
Notes
- Recommended cap should reflect expected traffic

## Add sort param for markets list
Description
Add explicit sorting support for market list responses.
Directory: apps/api
Background
Predictable sorting enables stable pagination and user-defined ordering.
Acceptance criteria
- `sort` param supports at least two fields
- Invalid sort values are rejected
- Sort direction is explicit (`asc`/`desc`)
Notes
- Ensure indexed columns back supported sort fields

## Add `GET /v1/markets/:id` endpoint
Description
Implement market details endpoint by unique market identifier.
Directory: apps/api
Background
Frontend needs complete details for market view and trading interface.
Acceptance criteria
- Returns single market payload by ID
- Returns 404 for unknown ID
- Includes market state and outcome metadata
Notes
- Keep payload shape stable for SDK consumers

## Add market not-found response
Description
Standardize not-found error semantics for market-specific endpoints.
Directory: apps/api
Background
Consistent 404 handling improves client reliability and debuggability.
Acceptance criteria
- Unknown market uses common error envelope
- Error code is deterministic (e.g., `MARKET_NOT_FOUND`)
- Covered by API test
Notes
- Reuse this pattern for other entity endpoints

## Add `GET /v1/markets/:id/orderbook` endpoint
Description
Expose market orderbook/depth snapshot from indexed data.
Directory: apps/api
Background
Orderbook visibility is critical for pricing transparency.
Acceptance criteria
- Endpoint returns bids/asks arrays
- Includes snapshot timestamp/ledger sequence
- Handles empty orderbook gracefully
Notes
- Start with read-only snapshot, not streaming

## Add response DTOs for market routes
Description
Define typed DTOs for all market route responses.
Directory: apps/api
Background
Typed contracts reduce accidental response drift.
Acceptance criteria
- DTOs created for list/details/orderbook
- Controllers map internal model to DTO
- Type checks pass in CI
Notes
- Keep internal entities decoupled from public DTOs

## Add `GET /v1/positions/:wallet` endpoint
Description
Implement wallet positions endpoint with current exposure by market.
Directory: apps/api
Background
Users need portfolio-level visibility for decision making.
Acceptance criteria
- Endpoint accepts wallet identifier
- Returns per-market exposure rows
- Uses standardized success response
Notes
- Add caching later if needed

## Validate wallet param format
Description
Validate wallet path parameter format before querying storage.
Directory: apps/api
Background
Input validation prevents malformed requests and unnecessary DB load.
Acceptance criteria
- Invalid wallet format returns 400
- Validation rule documented
- Covered by unit/integration tests
Notes
- Align validation with Stellar address formats

## Add `GET /v1/trades/:wallet` endpoint
Description
Return trade history for a wallet with deterministic ordering.
Directory: apps/api
Background
Trade history powers portfolio analytics and transparency.
Acceptance criteria
- Endpoint returns wallet trades
- Default sort is latest-first
- Includes pagination metadata
Notes
- Ensure response includes market identifiers

## Add date-range filter for trades
Description
Support `from` and `to` query filters for wallet trade history.
Directory: apps/api
Background
Time-window queries are needed for analytics and export workflows.
Acceptance criteria
- `from`/`to` filters accepted and validated
- Invalid date ranges return 400
- Filtered query uses indexes where possible
Notes
- Use UTC timestamps consistently

## Add market filter for trades
Description
Allow filtering wallet trades by market identifier.
Directory: apps/api
Background
Users frequently inspect one market's execution history.
Acceptance criteria
- `marketId` filter supported
- Invalid/unknown market filters handled safely
- Combined with date filters works correctly
Notes
- Keep query plans efficient

## Add PnL field in positions response
Description
Add total PnL summary in positions endpoint payload.
Directory: apps/api
Background
PnL is a primary metric users expect in prediction market apps.
Acceptance criteria
- Positions response includes `pnlTotal`
- Value type and currency/unit documented
- Calculations deterministic for same snapshot
Notes
- Use snapshot-based computation inputs

## Add unrealized PnL field
Description
Include unrealized PnL metric for open exposures.
Directory: apps/api
Background
Unrealized performance helps users assess current risk/reward.
Acceptance criteria
- Response includes `pnlUnrealized`
- Derived from latest indexed price data
- Null-safe when no open positions
Notes
- Document pricing source used in calculation

## Add realized PnL field
Description
Include realized PnL metric from closed/resolved positions.
Directory: apps/api
Background
Separating realized from unrealized clarifies portfolio outcomes.
Acceptance criteria
- Response includes `pnlRealized`
- Derived from closed trade/settlement records
- Test covers mixed open/closed positions
Notes
- Preserve precision for financial values

## Add empty-state response for new wallets
Description
Return a valid empty payload for wallets with no activity.
Directory: apps/api
Background
Clients should not need special-case handling for first-time users.
Acceptance criteria
- Endpoint returns 200 with empty list and zero totals
- No internal errors when wallet absent from tables
- Shape matches non-empty responses
Notes
- Avoid 404 for no-activity wallets

## Add pagination for trades endpoint
Description
Implement page/limit pagination for wallet trade history.
Directory: apps/api
Background
Trade history can grow large and must be retrieved incrementally.
Acceptance criteria
- Supports `page` and `limit`
- Returns `total` and `hasNext` metadata
- Stable ordering across pages
Notes
- Cursor pagination can be future enhancement

## Create indexer bootstrap file
Description
Add entrypoint that initializes indexer dependencies and starts ingestion loop.
Directory: apps/indexer
Background
A clear bootstrap flow improves observability and restart behavior.
Acceptance criteria
- Bootstrap initializes config/logger/storage client
- Ingestion loop starts from persisted cursor
- Startup and shutdown logs are emitted
Notes
- Include graceful shutdown hooks

## Add block cursor table
Description
Create persistence for indexer checkpoint/cursor state.
Directory: apps/indexer
Background
Without durable checkpoints, restarts can duplicate or skip processing.
Acceptance criteria
- Table/schema for cursor exists
- Read/write cursor operations implemented
- Cursor updates are transactional
Notes
- Support multi-network future by keying network ID

## Add last-processed-ledger storage
Description
Persist latest successfully indexed ledger sequence.
Directory: apps/indexer
Background
Ledger progress tracking drives lag monitoring and replay behavior.
Acceptance criteria
- Last indexed ledger saved after successful batch
- Value exposed via internal metrics service
- Value survives process restarts
Notes
- Keep write frequency balanced with throughput

## Add event fetch service
Description
Implement service that fetches raw chain events by ledger window.
Directory: apps/indexer
Background
Event retrieval is the first stage of indexer pipeline correctness.
Acceptance criteria
- Fetch supports start/end ledger inputs
- Handles pagination/retry on transient failures
- Emits telemetry for fetched event counts
Notes
- Keep transport concerns isolated from parsing logic

## Add market-created event parser
Description
Parse and normalize market creation events into internal model.
Directory: apps/indexer
Background
Market metadata must be captured accurately at inception.
Acceptance criteria
- Parser maps raw event fields to typed object
- Validation fails safely on malformed payloads
- Unit tests cover valid and invalid samples
Notes
- Store original payload for debugging

## Add trade-executed event parser
Description
Parse trade execution events into normalized trade records.
Directory: apps/indexer
Background
Trade data underpins positions, PnL, and volume analytics.
Acceptance criteria
- Parser handles both buy/sell direction
- Precision-safe numeric parsing implemented
- Unit tests include edge-case payloads
Notes
- Avoid floating-point precision loss

## Add market-resolved event parser
Description
Parse market resolution/finalization events for settlement state.
Directory: apps/indexer
Background
Resolution events are critical for payout and final PnL computation.
Acceptance criteria
- Resolution outcome extracted and validated
- Unknown outcome values fail safely
- Parsed records linked to market ID
Notes
- Include event source ledger in stored record

## Add idempotency key for each event
Description
Generate deterministic idempotency key for every processed event.
Directory: apps/indexer
Background
Idempotency prevents corruption when batches are retried or replayed.
Acceptance criteria
- Key formula documented and implemented
- Keys persisted with processed events
- Duplicate key insertion handled gracefully
Notes
- Include ledger + tx + event index in key

## Skip duplicate events safely
Description
Ensure duplicate events are no-ops instead of failures.
Directory: apps/indexer
Background
Retries and replays are expected in distributed ingestion pipelines.
Acceptance criteria
- Duplicate events detected by idempotency key
- Pipeline continues processing subsequent events
- Duplicate count metric/log emitted
Notes
- Maintain exactly-once effect at storage layer

## Add indexer heartbeat log
Description
Emit periodic heartbeat with cursor position and lag indicators.
Directory: apps/indexer
Background
Heartbeats help operators quickly verify indexer liveness/progress.
Acceptance criteria
- Heartbeat emitted at fixed interval
- Includes last indexed ledger and processing rate
- Structured log format used
Notes
- Tune interval to avoid log noise

## Create oracle service interface
Description
Define interface contract for oracle data providers.
Directory: apps/oracle
Background
Provider abstraction is required for fallback and provider migration.
Acceptance criteria
- Interface defines required methods and response schema
- Primary and fallback adapters implement interface
- Interface includes confidence/source metadata
Notes
- Keep methods domain-focused, not provider-specific

## Add primary provider adapter
Description
Implement adapter for primary oracle data source.
Directory: apps/oracle
Background
A production oracle flow needs an explicit preferred provider.
Acceptance criteria
- Adapter conforms to oracle interface
- Provider errors mapped to internal error types
- Adapter has integration test with mocked response
Notes
- Add circuit-breaker in later issue if needed

## Add secondary fallback adapter
Description
Implement fallback provider adapter used when primary fails.
Directory: apps/oracle
Background
Fallback improves resilience during provider outages or data anomalies.
Acceptance criteria
- Fallback adapter implements same interface
- Oracle service switches on primary failure
- Fallback usage is logged/metriced
Notes
- Preserve source attribution in final record

## Add provider timeout handling
Description
Add request timeouts and cancellation for provider calls.
Directory: apps/oracle
Background
Hanging provider requests can stall resolution workflows.
Acceptance criteria
- Provider call timeout configurable
- Timeout errors handled without process crash
- Timeout metrics/logs emitted
Notes
- Use shared timeout utility for consistency

## Add provider retry policy
Description
Implement bounded retries with backoff for transient provider failures.
Directory: apps/oracle
Background
Transient network errors should not immediately fail resolution jobs.
Acceptance criteria
- Retry count/backoff configurable
- Retries only for retryable error classes
- Final failure reported with context
Notes
- Avoid retry storms under provider outage

## Create resolution candidate table
Description
Add storage table for proposed market resolutions before finalization.
Directory: apps/oracle
Background
Resolution often needs review/challenge before becoming final.
Acceptance criteria
- Table stores market ID, proposed outcome, source, status
- Includes timestamps and operator metadata
- Migration and model definition added
Notes
- Keep immutable audit fields

## Add confidence score field
Description
Store confidence score for each oracle report and resolution candidate.
Directory: apps/oracle
Background
Confidence helps downstream policy decisions and alerting.
Acceptance criteria
- Field added to relevant schema/model
- Accepted score range validated
- API/internal consumers can read value
Notes
- Document scoring scale (e.g., 0-1 or 0-100)

## Add source attribution field
Description
Track which provider/source produced oracle data.
Directory: apps/oracle
Background
Attribution is required for transparency and debugging.
Acceptance criteria
- Source field persisted with oracle outputs
- Values are standardized identifiers
- Included in audit and metrics output
Notes
- Keep mapping table for provider aliases

## Add challenge window duration config
Description
Add config for resolution challenge period duration.
Directory: apps/oracle
Background
Challenge windows are core to transparent dispute handling.
Acceptance criteria
- Config variable documented and validated
- Resolution workflow respects configured window
- Tests cover before/after window behavior
Notes
- Use UTC timestamps for window calculations

## Add finalization job skeleton
Description
Create scheduled job scaffold for finalizing eligible resolutions.
Directory: apps/workers
Background
Finalization should be automated once challenge windows close.
Acceptance criteria
- Job runs on configurable interval
- Selects candidates eligible for finalization
- Emits structured execution logs
Notes
- Actual payout trigger can be separate issue

## Add API key auth middleware
Description
Implement API key authentication for internal/protected endpoints.
Directory: apps/api
Background
Operational and admin endpoints should not be publicly accessible.
Acceptance criteria
- Middleware validates configured API key
- Missing/invalid keys return 401
- Protected routes explicitly apply middleware
Notes
- Support key rotation in future iteration

## Add admin role constant
Description
Introduce explicit role constants for authorization checks.
Directory: packages/shared
Background
Stringly-typed role checks are error-prone and hard to audit.
Acceptance criteria
- Shared role constants/module created
- Admin role used in protected route guards
- Role list documented
Notes
- Keep roles minimal at MVP stage

## Protect admin-only routes
Description
Apply authorization guard to all admin/ops routes.
Directory: apps/api
Background
Unprotected operational routes can lead to privilege escalation.
Acceptance criteria
- Admin endpoints require authenticated admin role
- Unauthorized access returns 403
- Route inventory confirms no unguarded admin endpoints
Notes
- Add test matrix for role access

## Add unauthorized response helper
Description
Create standardized helper for 401 unauthorized responses.
Directory: apps/api
Background
Consistent auth error shapes simplify client handling.
Acceptance criteria
- Helper emits common error envelope
- Includes deterministic error code
- Used by auth middleware
Notes
- Do not leak auth internals in messages

## Add forbidden response helper
Description
Create standardized helper for 403 forbidden responses.
Directory: apps/api
Background
Authorization failures should be clear and consistent.
Acceptance criteria
- Helper emits common error envelope
- Includes `FORBIDDEN` code
- Used in role guard middleware
Notes
- Distinguish clearly from 401 responses

## Add basic request rate limiter
Description
Add baseline per-IP or per-key throttling middleware.
Directory: apps/api
Background
Rate limiting protects service stability under bursty traffic.
Acceptance criteria
- Limit window and threshold are configurable
- Exceeded requests return 429
- Response includes retry-after hint
Notes
- Start conservative and tune from metrics

## Add stricter limits for heavy endpoints
Description
Apply lower thresholds to resource-intensive endpoints.
Directory: apps/api
Background
Not all routes have equal cost; heavy routes need tighter controls.
Acceptance criteria
- Route-specific limits configured
- Heavy endpoints identified/documented
- Performance tests confirm reduced overload impact
Notes
- Keep policy transparent for integrators

## Add response header for rate-limit remaining
Description
Expose remaining quota and reset time in response headers.
Directory: apps/api
Background
Clients can self-throttle when quota visibility is available.
Acceptance criteria
- Header includes remaining request count
- Header includes reset timestamp/window
- Values are accurate across requests
Notes
- Use standard naming conventions where possible

## Add request body size limit
Description
Enforce max request payload size for API endpoints.
Directory: apps/api
Background
Large payloads can degrade performance and enable abuse.
Acceptance criteria
- Body size limit configured globally
- Oversized requests return 413
- Limit value documented in API docs
Notes
- Exceptions for file uploads can be separate issue

## Redact secrets from logs
Description
Implement log redaction for sensitive fields (keys, tokens, secrets).
Directory: packages/shared
Background
Unredacted logs are a major data leakage and compliance risk.
Acceptance criteria
- Redaction rules defined centrally
- Known sensitive keys are masked in logs
- Unit tests verify redaction behavior
Notes
- Review periodically as new sensitive fields appear

## Create initial migration framework setup
Description
Set up database migration tooling and base configuration.
Directory: packages/db
Background
Schema evolution must be reproducible and versioned across environments.
Acceptance criteria
- Migration tool configured in repo
- Commands for create/apply/rollback documented
- CI can run migration check step
Notes
- Pick tool already aligned with project stack

## Add migration for markets table
Description
Create schema migration for markets entity.
Directory: packages/db/migrations
Background
Market metadata is foundational for all frontend and analytics workflows.
Acceptance criteria
- Markets table includes id/status/timestamps core fields
- Primary key and basic constraints included
- Migration applies cleanly on empty DB
Notes
- Keep nullable fields minimal

## Add migration for outcomes table
Description
Create schema migration for market outcomes/options.
Directory: packages/db/migrations
Background
Outcome definitions are needed for pricing and settlement.
Acceptance criteria
- Outcomes table references market ID
- Supports multiple outcomes per market
- FK constraints enforce referential integrity
Notes
- Include display order field if needed

## Add migration for trades table
Description
Create schema migration for executed trade records.
Directory: packages/db/migrations
Background
Trades drive positions, volume, and historical analytics.
Acceptance criteria
- Trades table includes wallet, market, side, size, price
- Includes ledger/timestamp references
- Relevant constraints and indexes added
Notes
- Store numeric values with precision-safe types

## Add migration for positions table
Description
Create schema migration for wallet market positions snapshot/projection.
Directory: packages/db/migrations
Background
Fast position queries require optimized read storage.
Acceptance criteria
- Positions table keyed by wallet + market (+ outcome if needed)
- Includes quantity and valuation fields
- Supports upsert from indexer updates
Notes
- Clarify snapshot vs event-derived strategy in docs

## Add migration for oracle_reports table
Description
Create schema migration for raw oracle provider reports.
Directory: packages/db/migrations
Background
Raw report history supports auditing and dispute investigations.
Acceptance criteria
- Table stores source, payload hash, confidence, timestamps
- Links to market and candidate resolution if applicable
- Write path supports immutable inserts
Notes
- Consider retaining raw payload externally if large

## Add migration for resolutions table
Description
Create schema migration for finalized market resolutions.
Directory: packages/db/migrations
Background
Final resolution state is required for settlement and portfolio closeout.
Acceptance criteria
- Resolution table keyed by market ID
- Includes outcome, finalized at, and provenance fields
- Enforces one active final resolution per market
Notes
- Include correction/override metadata strategy

## Add indexes for market status queries
Description
Add indexes supporting common market listing filters.
Directory: packages/db/migrations
Background
List endpoints depend on efficient filtering by status and time.
Acceptance criteria
- Index on status + created_at (or equivalent) added
- Query plan shows index usage on list route
- No regression on write performance beyond acceptable threshold
Notes
- Revisit with real traffic patterns

## Add indexes for wallet lookups
Description
Add indexes for positions and trades by wallet address.
Directory: packages/db/migrations
Background
Portfolio queries are frequent and must remain low-latency.
Acceptance criteria
- Wallet indexes added to relevant tables
- Query latency improves for wallet endpoints
- Index definitions documented
Notes
- Consider composite indexes for wallet + market filters

## Add migration rollback documentation
Description
Document safe rollback procedure for failed schema deployments.
Directory: docs
Background
Rollback readiness reduces downtime during migration incidents.
Acceptance criteria
- Doc covers pre-checks, rollback command, and post-checks
- Includes data-loss warnings where relevant
- Linked from deployment runbook
Notes
- Keep procedure tested in staging

## Add unit test setup
Description
Set up unit test runner, config, and base helpers.
Directory: .
Background
Unit tests catch regressions early and enable safer refactoring.
Acceptance criteria
- Test framework configured and runnable in CI
- One sample test passes
- Coverage output enabled
Notes
- Keep tests deterministic and fast

## Add integration test setup
Description
Add integration test harness for API and storage interactions.
Directory: .
Background
Cross-layer behavior requires integration coverage beyond unit tests.
Acceptance criteria
- Integration test command available
- Test environment uses isolated DB
- Teardown cleans test data
Notes
- Prefer containerized dependencies in CI

## Test `GET /v1/health`
Description
Add integration test validating health endpoint response contract.
Directory: apps/api
Background
Monitoring depends on reliable health endpoint behavior.
Acceptance criteria
- Test asserts HTTP status and payload fields
- Test verifies fast response path
- Test runs in CI
Notes
- Keep assertions strict on required fields only

## Test `GET /v1/markets`
Description
Add integration test for market list endpoint baseline behavior.
Directory: apps/api
Background
Market listing is a high-traffic endpoint and core user entry point.
Acceptance criteria
- Test covers default pagination/sort behavior
- Test verifies response envelope
- Test validates empty and non-empty cases
Notes
- Seed deterministic fixtures for stable test outcomes

## Test `GET /v1/markets/:id`
Description
Add integration test for market details endpoint.
Directory: apps/api
Background
Detail endpoint reliability is critical for trading interfaces.
Acceptance criteria
- Test covers existing market response
- Test covers not-found path and error code
- Test validates schema shape
Notes
- Include assertions for key nested fields

## Test wallet positions endpoint
Description
Add integration tests for `GET /v1/positions/:wallet`.
Directory: apps/api
Background
Portfolio correctness directly impacts user trust.
Acceptance criteria
- Tests cover wallet with data and no data
- Asserts PnL fields and totals are present
- Invalid wallet format returns 400
Notes
- Use fixed-precision assertions for numeric values

## Test duplicate indexer event handling
Description
Add tests verifying duplicate events do not corrupt state.
Directory: apps/indexer
Background
Idempotency is a critical indexer correctness guarantee.
Acceptance criteria
- Replaying same event batch keeps state unchanged
- Duplicate count metric/log is emitted
- Processing continues after duplicates
Notes
- Include ledger replay scenario in tests

## Add CI lint job
Description
Add CI workflow step for linting and static code standards.
Directory: .github/workflows
Background
Automated style checks reduce review churn and maintain code quality.
Acceptance criteria
- CI includes lint job on push/PR
- Job fails build on lint errors
- Runtime is acceptable for contributor workflow
Notes
- Cache dependencies to keep CI fast

## Add CI test job
Description
Add CI workflow step to run unit and integration tests.
Directory: .github/workflows
Background
Automated testing gate prevents regressions from merging.
Acceptance criteria
- CI runs test suite on pull requests
- Failing tests block merge
- Test artifacts/logs are retained for debugging
Notes
- Consider matrix builds as project grows

## Add incident runbook starter doc
Description
Create operations runbook starter for common backend incidents.
Directory: docs/runbooks
Background
Documented response steps reduce downtime and uncertainty during incidents.
Acceptance criteria
- Runbook includes indexer lag, RPC outage, and DB incident sections
- Includes severity classification and escalation steps
- Linked from root README or ops docs
Notes
- Keep runbook actionable and regularly updated
