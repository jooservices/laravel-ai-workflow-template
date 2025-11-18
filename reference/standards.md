# Standards Reference

Quick lookup for all concrete rules, coverage targets, tool configurations, and exact requirements.

---

## Runtime & Type Safety Standards

### Runtime Requirements

- ✅ **MUST:** PHP version **8.4+** (minimum).  
- ✅ **MUST:** Laravel version **12.x** (minimum).  

### Type Safety (Recommended Requirements)
- ✅ **SHOULD:** `declare(strict_types=1);` in application code (classes, services, controllers, repositories)
- ⚠️ **OPTIONAL:** `declare(strict_types=1);` in config files, migrations, seeders (recommended but not mandatory)
- ✅ **SHOULD:** Explicit types on all method parameters and return values
- ✅ **SHOULD:** `final` keyword for classes by default (allows exceptions for extensible base classes)
- ✅ **SHOULD:** `readonly` modifier for constructor-injected dependencies (allows exceptions when mutation is needed)
- ⚠️ **AVOID:** `mixed` type (allowed with inline justification for third-party interfaces or legacy code)

### PHPDoc Requirements
- ✅ **SHOULD:** Document array shapes: `@param array<string, mixed> $data`
- ✅ **SHOULD:** Document collection types: `@return array<int, User>`

### Tool Configuration
- PHPStan level: `8-9` (allow suppressions with justification)
- PHPStan rules: `checkMissingIterableValueType: true`, `checkGenericClassInNonGenericObjectType: true`
- **Suppressions:** Allowed with inline comments explaining why

---

## PHP Coding Standards (Behavioral)

### Exceptions & Error Handling

- ✅ **MUST:** Use a small, project-specific exception hierarchy (e.g., `DomainException`, `ValidationException`, `InfrastructureException`) instead of generic `\Exception` for domain logic.  
- ✅ **MUST:** Throw typed exceptions to represent error conditions; do not use magic return codes or `null` for exceptional flows.  
- ✅ **MUST:** Log or rethrow when catching `\Throwable` or broad exceptions in infrastructure layers.  
- ❌ **FORBIDDEN:** Swallowing exceptions silently (empty `catch` blocks).  
- ❌ **FORBIDDEN:** Using error suppression (`@`) in production code.  

### Dependency Management & Globals

- ✅ **MUST:** Use constructor injection and Laravel’s container for all non-trivial dependencies (services, repositories, SDKs, loggers).  
- ✅ **MUST:** Depend on interfaces/contracts in public APIs wherever reasonable.  
- ❌ **FORBIDDEN:** Manually instantiating services in controllers or other framework-managed classes (e.g., `new SomeService()` instead of DI).  
- ❌ **FORBIDDEN:** Static service methods for business logic (static-only utility classes are allowed for pure, stateless helpers).  
- ❌ **FORBIDDEN:** Using global state as a coordination mechanism (e.g., writable singletons, global variables).

### Comparisons & Null Handling

- ✅ **MUST:** Use strict comparisons (`===`, `!==`) for booleans, integers, and strings in business logic.  
- ✅ **MUST:** Model optional values explicitly in types (`string|null`, `?int`) and handle them deliberately.  
- ❌ **FORBIDDEN:** Loose equality (`==`, `!=`) in security-, money-, or permission-related code paths.  
- ❌ **FORBIDDEN:** Using magic sentinel values (e.g., `-1`, `0`, `''`) instead of proper nullable types or enums.

### Enums & Value Objects

- ✅ **MUST:** Use PHP backed enums for fixed sets of options (statuses, types, roles) instead of plain strings/ints in domain logic.  
- ✅ **SHOULD:** Introduce small value objects (e.g., `Email`, `Money`, `Percentage`) where domain correctness is important.  
- ❌ **FORBIDDEN:** “Stringly-typed” domain concepts passed around as unvalidated strings/ints (e.g., `'active'`, `'draft'`, `'paid'`).  

### Runtime Configuration Expectations

- ✅ **MUST:** Run production with `display_errors=Off` and `log_errors=On`; errors must be logged, not shown.  
- ✅ **MUST:** Use a single canonical timezone (UTC) at the PHP/Laravel level; store and manipulate times as `DateTimeImmutable`/CarbonImmutable.  
- ❌ **FORBIDDEN:** Modifying global `error_reporting` or timezone settings inside application code (configure via php.ini/env/bootstrap instead).

---

## Quality Pipeline Standards

### Tool Execution Order (MANDATORY)
1. **Laravel Pint** → `composer lint:pint`
2. **PHP_CodeSniffer** → `composer lint:phpcs`
3. **PHPMD** → `composer analyze:phpmd`
4. **PHPStan** → `composer analyze:phpstan`

### Commands
```bash
composer lint              # All tools in order
composer lint:pint         # Laravel Pint only
composer lint:phpcs        # PHPCS only
composer analyze:phpmd     # PHPMD only
composer analyze:phpstan   # PHPStan only
```

### Configuration Files
- Laravel Pint: `pint.json`
- PHPCS: `phpcs.xml`
- PHPMD: `phpmd.xml`
- PHPStan: `phpstan.neon.dist`

### Requirements
- ✅ **SHOULD:** All tools pass before commit (zero violations for new code)
- ⚠️ **ALLOWED:** Suppressions with justification for legacy code or third-party integrations
- ⚠️ **ALLOWED:** Global suppressions in config files with documented justification
- ✅ **SHOULD:** Line-level suppressions include inline comments explaining why

---

## Test Coverage Standards

### Coverage Targets (CI-Enforced)

| Layer | Minimum Coverage | 
|-------|-----------------|
| **Overall Project** | **70%** |
| **Core Module Services** | **70%** |
| **API Controllers** | **70%** |
| **FormRequests** | **70%** |
| **Models (business logic)** | **70%** |
| **Repositories** | **70%** |
| **Middleware** | **70%** |
| **SDK Classes** | **70%** |

### Coverage Exclusions
- Service Providers (framework boilerplate)
- Migrations (schema definitions)
- Configuration files (data, not logic)
- Blade templates (frontend presentation)

### Commands
```bash
composer test                 # Run all tests
composer test:coverage        # Generate HTML coverage report
composer test:coverage-check  # Enforce coverage thresholds (CI gate)
```

### New Code Requirements
- ✅ **SHOULD:** Every new class have accompanying unit tests before merge
- ⚠️ **ALLOWED:** Exceptions for simple DTOs, Value Objects, or data transfer classes
- ✅ **SHOULD:** PRs that decrease coverage below 70% threshold be addressed
- ✅ **SHOULD:** Coverage gaps in modified code addressed in same PR (or follow-up PR for urgent fixes)

### Test File Naming
- Unit tests: `tests/Unit/{Namespace}/{ClassName}Test.php`
- Feature tests: `tests/Feature/{Module}/{Feature}Test.php`
- Test methods: `test_{method_name}_{scenario}_{expected_outcome}()`

---

## Mocking Standards

### What to Mock

- ✅ **MUST mock** external dependencies in unit tests:
  - Third‑party SDKs and HTTP clients.
  - Infrastructure services (mail, queues, storage, external caches).
  - Cross‑module services when testing one module in isolation.
- ❌ **MUST NOT mock**:
  - The class under test itself.
  - Core framework primitives (basic Eloquent operations, Laravel validation) in feature tests.

### Where to Mock

- **Unit tests**:
  - ✅ Mock all collaborators of the unit under test (repositories, SDK contracts, loggers, events, queues).  
  - ✅ Assert behavior and interactions (methods called, parameters passed), not just return values.
- **Feature tests**:
  - ✅ Use real HTTP and database.  
  - ✅ Mock external services/SDKs to avoid real network calls and side effects.  
  - ❌ DO NOT mock controllers or middleware in feature tests.
- **Integration tests (if used)**:
  - ✅ Use real services within your process boundary.  
  - ✅ Stub only truly external systems that cannot be run locally.

### How to Mock

- ✅ Prefer **constructor‑injected contracts** as seams (`SdkContract`, `RepositoryContract`, `ServiceContract`).  
- ✅ Use mocks/spies/fakes to verify interactions (`expects`, `shouldReceive`, `shouldHaveReceived`).  
- ✅ Prefer behavioral assertions over deep implementation detail assertions (e.g., don’t assert on private method calls).  
- ❌ Avoid over‑mocking: if a dependency is simple and stable (e.g., value objects, DTOs), use the real implementation.

### Special Rules

- **Jobs & Events**:
  - ✅ MUST pass IDs/UUIDs, not Eloquent models, into Jobs.  
  - ✅ In Job tests, mock repositories/services the Job depends on; assert behavior and error handling.
- **SDKs**:
  - ✅ SDKs themselves MUST be thoroughly unit‑tested with clear input/output behavior.  
  - ✅ In other layers (services, controllers), SDKs MUST be mocked or faked to avoid real external calls.
- **Logging**:
  - ✅ It is acceptable to mock `ActionLogger` (or similar logging contracts) to assert that critical operations are logged once per mutation.  

Mocking must always support the architectural rules defined in `architecture/principles.md` and `architecture/flow.md`; tests must verify behavior at the correct layer boundaries rather than coupling to internal implementation details.

---
## Cache Backend Selection

### Default Drivers by Environment

- **Local / Development (`APP_ENV=local`)**  
  - ✅ **SHOULD:** Use `CACHE_DRIVER=file` for simplicity and zero extra infrastructure.  
  - ✅ **MUST:** Write code that assumes a production cache backend (Redis) will be used in higher environments (no driver‑specific logic in code).

- **Production / Staging**  
  - ✅ **SHOULD:** Use `CACHE_DRIVER=redis` as the default cache backend.  
  - ✅ **MAY:** Temporarily use `CACHE_DRIVER=database` for small deployments that do not yet have Redis, but MUST plan to move to Redis for higher traffic.

### When to Use Each Backend

- **File cache (`file` driver)**  
  - ✅ **Use for:** Local development or single‑instance test environments.  
  - ❌ **Do not use for:** Multi‑instance production deployments, high‑volume caching, or shared state.

- **Database cache (`database` driver)**  
  - ✅ **Use for:** Small apps needing shared cache across instances when Redis is not available.  
  - ❌ **Do not use for:** High‑churn or high‑volume cache scenarios that would compete with main DB workload.

- **Redis cache (`redis` driver)**  
  - ✅ **Use for:** Production and staging environments as the primary cache backend.  
  - ✅ **Use for:** High‑traffic/hot paths (query results, external API responses, computed data).  
  - ❌ **Do not use for:** Data that must be persisted long‑term; always handle cache misses and treat Redis as ephemeral.

### Code-Level Rules

- ✅ **MUST:** Select cache backend via configuration (`CACHE_DRIVER`) and environment, not in application code.  
- ✅ **MUST:** Use the `Cache` facade or cache contracts; do NOT hard‑code specific drivers in Services.  
- ✅ **MUST:** Implement application‑level caching only in the Service layer (or dedicated caching helpers used by Services).  
- ✅ **MUST:** Handle cache misses gracefully (never assume cache hit).  
- ❌ **FORBIDDEN:** Choosing or switching cache drivers inside business logic.  

---
## Module Standards

### Naming Convention
- ✅ **MUST:** Singular PascalCase names matching business domain
- ✅ **Correct:** `{DomainName}`, `{BusinessDomain}`, `{ServiceName}`
- ❌ **Wrong:** `Posts` (technical feature), `{ServiceName}Integration` (redundant)

---

## Event Standards

### Responsibilities

- ✅ **MUST:** Represent a domain or system event (something that *happened*), not execute business logic.  
- ✅ **MUST:** Be immutable data carriers (public readonly properties or constructor‑only assignment).  
- ✅ **MUST:** Use descriptive, past‑tense names (`UserRegistered`, `OrderShipped`, `InferenceStreamed`).  
- ❌ **FORBIDDEN:** Calling external APIs directly from event classes.

### Listeners

- ✅ **MUST:** Contain minimal orchestration logic (e.g., send notifications, dispatch Jobs).  
- ✅ **MAY:** Dispatch Jobs for heavy or slow work (email, reports, AI calls).  
- ❌ **FORBIDDEN:** Embedding complex business workflows or cross‑aggregate logic in listeners; that belongs in Services.  
- ❌ **FORBIDDEN:** Direct database writes that bypass Service/Repository rules.

---

## Observer Standards

### Responsibilities

- ✅ **MUST:** React to model lifecycle events (created, updated, deleted) with simple side effects.  
- ✅ **MAY:** Log actions, dispatch events, or dispatch Jobs.  
- ❌ **FORBIDDEN:** Containing core business logic – observers should delegate to Services when domain rules are required.  
- ❌ **FORBIDDEN:** Calling external APIs directly; use Jobs/Services instead.

### Usage

- ✅ **MUST:** Keep observers thin and focused on cross‑cutting concerns (e.g., audit logging, notifications).  
- ✅ **MUST:** Register observers in Service Providers or dedicated bootstrapping locations.  
- ✅ **SHOULD:** Prefer explicit Services + Events when behavior grows beyond simple model hooks.

---

## Naming Conventions

### Quick Reference

| Component | Pattern | Example |
|-----------|---------|---------|
| **Job** | `<Verb><Name>Job` | `Process{ServiceName}Job` |
| **Event** | `<Name><PastParticiple>` | `{ServiceName}InferenceStreamed` |
| **Interface/Contract** | `<Name>Contract` | `SdkContract` |
| **Service** | `<Name>Service` | `CategoryService` |
| **Repository** | `<Name>Repository` | `TokenRepository` |
| **Controller** | `<Name>Controller` | `CategoryController` |
| **FormRequest** | `<Action><Name>Request` | `StoreCategoryRequest` |
| **Resource** | `<Name>Resource` | `CategoryResource` |
| **Policy** | `<Name>Policy` | `CategoryPolicy` |
| **Command** | `<Name>Command` | `{ServiceName}PingCommand` |
| **Model** | `<Name>` (singular) | `{ServiceName}Job` |

### Rules (PHP/PSR-12 Standards)
- ✅ **MUST:** PascalCase for class names (e.g., `CategoryService`)
- ✅ **MUST:** camelCase for method names (e.g., `createUser()`)
- ✅ **MUST:** camelCase for variable names (e.g., `$userId`)
- ✅ **MUST:** UPPER_SNAKE_CASE for constants (e.g., `MAX_RETRY_COUNT`)

### Variable Naming Convention

**Core Principle:** Simple but meaningful names that clearly express purpose.

#### Basic Rules:
- ✅ **MUST:** Use camelCase for all variables (e.g., `$userId`, `$categoryName`, `$isActive`)
- ✅ **MUST:** Use descriptive, meaningful names (e.g., `$userCount` not `$uc`, `$categoryId` not `$catId`)
- ✅ **MUST:** Use full words, avoid abbreviations unless universally understood (e.g., `$id`, `$url`, `$httpClient`)
- ❌ **FORBIDDEN:** Single-letter variables except in loops (`$i`, `$j`, `$k` for iterators)
- ❌ **FORBIDDEN:** Abbreviated names that require context to understand (`$usr`, `$cat`, `$svc`)

#### Examples:

✅ **CORRECT:**
```php
$userId = 42;
$categoryName = 'Technology';
$isActive = true;
$hasPermission = false;
$userCount = 10;
$categoryIds = [1, 2, 3];
$httpClient = app(HttpClient::class);
```

❌ **WRONG:**
```php
$uid = 42;              // Use $userId
$cat = 'Technology';    // Use $categoryName
$act = true;            // Use $isActive
$usrCnt = 10;           // Use $userCount
$catIds = [1, 2, 3];    // Use $categoryIds
```

### Rules (Project-Specific)
- ✅ **MUST:** Singular for Models, Services, Repositories
- ✅ **MUST:** Descriptive verbs for Jobs (`Process`, `Send`, `Generate`)
- ✅ **MUST:** Past tense for Events (`Streamed`, `Created`, `Updated`)
- ❌ **FORBIDDEN:** Abbreviations (`{Abbr}Sdk` → `{ServiceName}Sdk`)
- ❌ **FORBIDDEN:** Plural in class names (`CategoriesService` → `CategoryService`)

> **Complete Guide:** See [Laravel Components & Patterns Guide](../guides/laravel-components-patterns.md#naming-conventions) for detailed examples and patterns.

---

## Constants Usage Policy

### ✅ MUST: Use Constants (Not Magic Numbers)

**Rules:**
- ✅ **MUST:** Use constants for all HTTP status codes
- ✅ **MUST:** Use constants for all numeric values that have semantic meaning
- ✅ **MUST:** Use `Illuminate\Http\Response` constants for HTTP status codes
- ❌ **FORBIDDEN:** Magic numbers in code (200, 201, 404, 500, etc.)
- ❌ **FORBIDDEN:** Hardcoded numeric values without constants

### HTTP Status Code Constants

**Required Import:**
```php
use Illuminate\Http\Response;
```

**Available Constants:**
- `Response::HTTP_OK` (200) - Successful GET, PUT, PATCH
- `Response::HTTP_CREATED` (201) - Successful POST (resource created)
- `Response::HTTP_NO_CONTENT` (204) - Successful DELETE
- `Response::HTTP_BAD_REQUEST` (400) - Invalid request, business logic error
- `Response::HTTP_UNAUTHORIZED` (401) - Missing or invalid authentication
- `Response::HTTP_FORBIDDEN` (403) - Authenticated but not authorized
- `Response::HTTP_NOT_FOUND` (404) - Resource not found
- `Response::HTTP_UNPROCESSABLE_ENTITY` (422) - Validation failed
- `Response::HTTP_TOO_MANY_REQUESTS` (429) - Rate limit exceeded
- `Response::HTTP_INTERNAL_SERVER_ERROR` (500) - Server error

### Examples

```php
use Illuminate\Http\Response;
use App\Http\Responses\ApiResponse;

// ✅ CORRECT: Use constants
return ApiResponse::success(
    code: 'resource.created',
    message: 'Resource created successfully',
    data: $resource->toArray(),
    status: Response::HTTP_CREATED
);

return ApiResponse::error(
    code: 'resource.validation_failed',
    message: 'Invalid data',
    status: Response::HTTP_UNPROCESSABLE_ENTITY
);

return ProductResource::make($product)
    ->response()
    ->setStatusCode(Response::HTTP_CREATED);

return response()->noContent();  // Automatically uses Response::HTTP_NO_CONTENT

// ❌ WRONG: Magic numbers
return ApiResponse::success(..., status: 201);  // FORBIDDEN
return ApiResponse::error(..., status: 400);  // FORBIDDEN
->setStatusCode(201);  // FORBIDDEN
```

### Other Constants

For other numeric values with semantic meaning, create project-specific constants:

```php
// ✅ CORRECT: Define constants for semantic values
final class PaginationLimits
{
    public const DEFAULT_PER_PAGE = 15;
    public const MAX_PER_PAGE = 100;
    public const MIN_PER_PAGE = 1;
}

// Usage
$perPage = min($request->get('per_page', PaginationLimits::DEFAULT_PER_PAGE), PaginationLimits::MAX_PER_PAGE);
```

---

## Job Data Passing Policy

### ✅ MUST: Pass ID/UUID, NOT Model Instances

**Security and Data Integrity Rules:**

1. ✅ **MUST:** Pass primitive types (string, int, array) to Job constructor
2. ✅ **MUST:** Fetch Model in `handle()` method
3. ✅ **MUST:** Handle missing data gracefully (return early, do not throw)
4. ✅ **MUST:** Remove `SerializesModels` trait if not passing Model
5. ❌ **FORBIDDEN:** Pass Model instances to Job constructor
6. ❌ **FORBIDDEN:** Pass sensitive data (passwords, tokens) to Job

### Job Parameter Naming

| Type | Pattern | Example |
|------|---------|---------|
| Single ID | `$<name>Id` | `$userId`, `$orderId` |
| UUID | `$<name>Uuid` | `$jobUuid`, `$orderUuid` |
| Multiple IDs | `$<name>Ids` | `$categoryIds`, `$productIds` |

### Example

```php
// ✅ GOOD: Pass UUID
final class Process{ServiceName}Job implements ShouldQueue
{
    public function __construct(private readonly string $jobUuid) {}
    
    public function handle(): void
    {
        $job = {ServiceName}Job::where('uuid', $this->jobUuid)->first();
        if ($job === null) {
            \Log::warning('Job not found', ['uuid' => $this->jobUuid]);
            return;  // Graceful handling
        }
        // Process...
    }
}

// ❌ BAD: Pass Model instance
final class Process{ServiceName}Job implements ShouldQueue
{
    use SerializesModels;  // ❌ Not needed
    
    public function __construct(private readonly {ServiceName}Job $job) {}
    
    public function handle(): void
    {
        // ❌ ModelNotFoundException if deleted
        $this->job->update([...]);
    }
}
```

> **Complete Guide:** See [Laravel Components & Patterns Guide](../guides/laravel-components-patterns.md#data-passing-policies) for detailed examples and security considerations.

### Module Creation
```bash
php artisan module:make {DomainName}
```

### Module Structure (MANDATORY)
```
Modules/{DomainName}/
├── Config/config.php              # MUST: Module configuration
├── Database/Migrations/           # MUST: Domain-specific migrations
├── Http/
│   ├── Controllers/               # MUST: API controllers (final classes)
│   └── Requests/                  # MUST: FormRequest validation
├── Models/                        # MUST: Domain models (final classes)
├── Providers/
│   └── {Domain}ServiceProvider.php # MUST: Service registration
├── Services/                      # MUST: Business logic (final classes)
│   └── Contracts/                 # MUST: Service interfaces
├── routes/api.php                 # MUST: API routes (auto-prefixed /api/v1)
└── module.json                    # MUST: Module metadata
```

### Module Decision Rule
**"Does this contain business-specific logic?"**
- **YES** → Business domain module ({ServiceName} SDK → {DomainModule} module)
- **NO** → Core module (ActionLogger → Core module)

### Module Activation
Configure in `modules_statuses.json`:
```json
{
    "Core": true,
    "{DomainModule}": true,
    "Twitter": false,
    "AI": true
}
```

---

## API Response Standards

### Response Envelope (MANDATORY)
```php
use App\Http\Responses\ApiResponse;

use Illuminate\Http\Response;

// Success
return ApiResponse::success(
    code: 'resource.created',
    message: 'Resource created successfully',
    data: $resource->toArray(),
    meta: ['timestamp' => now()],
    status: Response::HTTP_CREATED
);

// Error
return ApiResponse::error(
    code: 'resource.validation_failed',
    message: 'Invalid data',
    meta: ['errors' => $validator->errors()],
    data: [],
    status: Response::HTTP_UNPROCESSABLE_ENTITY
);
```

### Response Structure
```json
{
  "ok": true|false,
  "code": "domain.action",
  "status": 200,
  "message": "Human-readable message",
  "data": {},
  "meta": {}
}
```

### Resource vs Raw JSON
- ✅ **Use Resource:** Data maps to an Eloquent Model
- ✅ **Use Raw JSON:** Authentication responses, aggregated stats, confirmations

---

## Audit Logging Standards

### Action Log Channel
- **File:** `storage/logs/action.log`
- **Retention:** 30 days
- **Use:** Domain mutations (create, update, delete)
- **Format:** Strict structure with MANDATORY fields

**MANDATORY Fields:**
```php
[
    'operation' => 'user.updated',  // MANDATORY: Operation name (domain.action format)
    'actor' => [
        'id' => 42,  // MANDATORY if actor provided
        'type' => User::class,  // MANDATORY if actor provided
    ] | null,  // MANDATORY (can be null for system actions)
    'occurred_at' => '2025-01-01T12:00:00+00:00',  // MANDATORY: ISO 8601 timestamp (auto-generated)
]
```

**OPTIONAL Fields (with conditions):**
```php
[
    'before' => [...],  // REQUIRED for update operations, optional for create/delete
    'after' => [...],  // REQUIRED for create/update operations, optional for delete
    'metadata' => [...],  // REQUIRED for security events, optional for regular operations
]
```

**Conditions:**
- **`before`:** REQUIRED when logging update operations (to track state changes)
- **`after`:** REQUIRED when logging create/update operations (to track new state)
- **`metadata`:** REQUIRED for security events (login, permission changes, etc.)

**Complete Example:**
```php
use App\Logging\ActionLogger;

$logger->log(
    operation: 'user.updated',
    actor: auth()->user(),
    before: ['name' => 'Old Name'],
    after: ['name' => 'New Name'],
    metadata: ['ip' => request()->ip()]
);
```

**Resulting Log Entry:**
```php
Log::channel('action')->info('Domain action recorded', [
    'operation' => 'user.updated',
    'actor' => [
        'id' => 123,
        'type' => 'App\\Models\\User',
    ],
    'occurred_at' => '2025-01-15T10:30:45.123Z',
    'before' => ['name' => 'Old Name'],
    'after' => ['name' => 'New Name'],
    'metadata' => ['ip' => '127.0.0.1'],
]);
```

### General Log Channel
- **File:** `storage/logs/external.log` (or `general.log` in the future)
- **Retention:** 14 days
- **Use:** General errors, warnings, and info (not third-party requests)
- **Format:** Flexible structure, but MUST include minimum required fields

**MANDATORY Fields (Minimum Requirements):**
```php
[
    'timestamp' => '2025-01-15T10:30:45.123Z',  // MANDATORY: ISO 8601 timestamp (auto-added by Laravel)
    'level' => 'error|warning|info',  // MANDATORY: Log level (implicit from Log::error/warning/info)
    'message' => '...',  // MANDATORY: Human-readable message
]
```

**RECOMMENDED Fields:**
```php
[
    'service' => 'product|user|...',  // RECOMMENDED: Service name
    'context' => [...],  // RECOMMENDED: Additional context
    'user_id' => 123,  // RECOMMENDED: If applicable
]
```

**Note:** Format is flexible (you can add custom fields), but minimum required fields MUST be present. Laravel automatically adds timestamp and level.

**Examples:**
```php
// Controller error logging
Log::channel('external')->warning('{ExternalService} API error in controller', [
    'user_id' => auth()->id(),
    'endpoint' => $request->path(),
    'method' => $request->method(),
    'exception' => $exception->getMessage(),
    'context' => $exception->getContext(),
]);

// Service error logging
Log::channel('external')->error('Service error', [
    'service' => 'product',
    'operation' => 'create',
    'error' => $e->getMessage(),
]);
```

### Third-Party Request Log Channel
- **File:** `storage/logs/third-party-requests.log` (to be implemented)
- **Retention:** 14 days (configurable)
- **Use:** All third-party API requests/responses with complete information
- **Format:** Strict structure with MANDATORY fields

**MANDATORY Fields:**
```php
[
    'timestamp' => '2025-01-15T10:30:45.123Z',  // ISO 8601
    'service' => '{servicename1}|{servicename2}|{servicename3}|...',
    'method' => 'GET|POST|PUT|DELETE|PATCH',
    'endpoint' => '/v1/models',  // Full endpoint path
    'base_url' => 'http://localhost:1234',
    'status_code' => 200,
    'duration_ms' => 125,
    'success' => true|false,
    'request' => [
        'headers' => [...],  // Sanitized
        'query_params' => [...],
        'payload' => [...],  // Full request body
        'content_type' => 'application/json',
    ],
    'response' => [
        'headers' => [...],
        'body' => [...],  // Full response body
        'content_type' => 'application/json',
        'size_bytes' => 2048,
    ],
    'error' => [...],  // If applicable
    'metadata' => [
        'user_id' => 123,
        'request_id' => 'uuid-here',
    ],
]
```

**Note:** Third-party request logging will be implemented in a separate plan. Currently, SDKs are logging to the `external` channel with inconsistent formats.

### Requirements
- ✅ **MUST:** Log all domain mutations using ActionLogger
- ✅ **MUST:** Include actor, before/after state, metadata
- ✅ **MUST:** Mask sensitive data (passwords, tokens) in logs
- ✅ **MUST:** Use General Log for general errors/info
- ⚠️ **PLANNED:** Use Third-Party Request Log for all third-party API requests (standard format)

---

## Logging Storage & Observability Strategy

### File Logs (Baseline)

- ✅ **MUST:** Treat Laravel log channels (including Action, General, and Third‑Party Request logs) as the **primary write target**.  
- ✅ **MUST:** Write logs to `storage/logs/*.log` (or equivalent) and let infrastructure ship them to central log systems.  
- ❌ **FORBIDDEN:** Giving developers direct SSH access to production servers solely to read logs.

### Centralized Logging (Recommended)

- ✅ **SHOULD:** Configure infrastructure to collect `storage/logs/*.log` and send them to a central log system (e.g., ELK/Elastic, Loki/Grafana, CloudWatch, Datadog, etc.).  
- ✅ **MUST:** Give developers access to **centralized log dashboards/search**, not to production servers.  
- ✅ **MUST:** At minimum, centralize the `third-party-requests` channel once implemented so external API issues can be diagnosed from a single place.  
- ❌ **FORBIDDEN:** Relying on manual server access as the primary way to view production logs.

### Database-Backed Audit Logs (Optional, Recommended for Action Log)

- ✅ **MAY:** Persist Action Log entries into a dedicated database table (e.g., `audit_logs`) **in addition to** file logging, when you need queryable audit trails in the application UI.  
- ✅ **SHOULD:** Use a dedicated model/repository for audit logs; do not mix audit storage with domain models.  
- ✅ **MUST:** Keep sensitive data masked in database logs as well as in file logs.  
- ❌ **FORBIDDEN:** Storing high-volume general/third‑party logs in relational DB for routine logging (use file + central log system instead).

### Secure UI for Log Inspection (Optional)

- ✅ **MAY:** Build an internal, authenticated admin UI (or API) to read and filter **database-backed audit / third‑party request logs** for debugging and compliance.  
- ✅ **MUST:** Protect such UI with strong authorization (admin‑only or dedicated permissions).  
- ✅ **MUST:** Only display sanitized fields; never expose secrets, tokens, or full payloads that violate privacy/security rules.  
- ❌ **FORBIDDEN:** Exposing raw log files or arbitrary file readers in the admin UI.

---

## External Service Integration Standards (Example)

> **Example:** These standards show how to integrate with a third‑party REST API using an SDK and environment‑based configuration. Adapt the names and env vars to your own external services (e.g., payment provider, CMS, internal API).

### SDK Usage

- ✅ **MUST:** Use an SDK contract (e.g., `ExternalServiceSdkContract`), never Guzzle directly in services.  
- ✅ **MUST:** Proxy all external calls through Laravel – never call third‑party APIs directly from the frontend.  
- ✅ **MUST:** Expose clear SDK methods (e.g., `listItems()`, `createItem()`, `authenticate()`) that hide HTTP details.

### Configuration

- Base URL env var (e.g., `EXTERNAL_API_URL`)  
- API timeout env var (e.g., `EXTERNAL_API_TIMEOUT`, default 10 seconds)  
- User agent env var (e.g., `EXTERNAL_API_USER_AGENT`)  
- API namespace or version env var (e.g., `EXTERNAL_API_NAMESPACE`)  

### Token / Credential Management

- Store access tokens/credentials securely (e.g., hashed tokens in a dedicated table).  
- “Remember me” functionality is optional and must respect security requirements.  
- Auto‑inject Bearer/API keys via central resolver/middleware, not scattered through the codebase.

---

## Frontend Standards

### TypeScript Requirements
- ✅ **MUST:** TypeScript-only (no JavaScript files)
- ✅ **MUST:** Explicit types for all variables and functions
- ✅ **MUST:** Strict mode enabled in `tsconfig.json`

### UI/UX Standards
- ✅ **MUST:** Dark theme aesthetic on all surfaces
- ✅ **MUST:** Bootstrap + FontAwesome exclusively
- ✅ **MUST:** Binary toggles use switches, not checkboxes
- ✅ **MUST:** Loading states: disable controls + spinner + dim context
- ✅ **MUST:** Toast notifications: top-right, auto-dismiss (5s) non-fatal errors

### Layout Requirements
- ✅ **MUST:** All pages include Navbar component
- ✅ **MUST:** Active state indication on current page's parent/child nav items
- ✅ **MUST:** Container layout with `container-fluid` and Bootstrap grid

### Delete Action Pattern
- ✅ **MUST:** Delete buttons use `btn-danger` class (red styling)
- ✅ **MUST:** Confirmation modal before execution
- ✅ **MUST:** Modal includes: action description, item name, irreversibility warning
- ✅ **MUST:** FontAwesome icons: `fa-trash`, `fa-times`, etc.

### Error Handling
```typescript
try {
    await axios.post('/api/v1/resource', data);
    toast.success('Success message');
} catch (error) {
    if (axios.isAxiosError(error)) {
        toast.error(error.response?.data?.message || 'Error occurred', {
            autoClose: false  // Sticky for fatal errors
        });
    }
}
```

---

## Documentation Language Standards

### MANDATORY Requirements
- ✅ **MUST:** All documentation files (`.md` files in `docs/`) written in English only
- ✅ **MUST:** All code comments and PHPDoc annotations in English only
- ✅ **MUST:** All inline documentation, examples, and explanations in English only
- ✅ **MUST:** All commit messages in English (see [Git Standards](#git-standards))
- ❌ **FORBIDDEN:** Vietnamese or any other non-English language in documentation
- ❌ **FORBIDDEN:** Mixed languages (English + Vietnamese) in same document

### Scope
This policy applies to:
- All `.md` files in `docs/` directory
- All PHPDoc annotations (`@param`, `@return`, `@throws`, etc.)
- All inline code comments
- All commit messages
- All README files
- All code examples and explanations

### Exception
- **Communication:** Team members can communicate in any language (chat, discussions)
- **Documentation:** All written documentation must be English only

> **Complete Guide:** See [Documentation as Code Principle](../architecture/principles.md#21-documentation-as-code) for detailed requirements.

---

## Service Layer Standards

### Flow Pattern
`Controller → Service → Repository/SDK`

### Service Rules
- ✅ **MUST:** One service = one business logic domain
- ✅ **MUST:** Services call SDK directly (no repository for external APIs)
- ✅ **MUST:** Use Repository only for local database operations
- ✅ **MUST:** Inject other services for different business logic domains

### Repository Rules
- ✅ **MUST:** Create repositories only for database access
- ✅ **MUST:** Return Models or null
- ❌ **FORBIDDEN:** Business logic in repositories
- ❌ **FORBIDDEN:** Repositories for external APIs

### FormRequest Rules
- ✅ **MUST:** All validation in FormRequest classes
- ✅ **MUST:** Typed `rules()` method: `@return array<string, array<int, string|Rule>>`
- ✅ **MUST:** 100% test coverage for FormRequests

---

## Git Standards

### Commit Discipline
- ✅ **MUST:** Only commit files you directly created/modified
- ❌ **FORBIDDEN:** `git add .` or `git add -A` (commits others' work)
- ✅ **MUST:** Use explicit file paths: `git add specific-file.php`

### Pre-commit Checklist
1. `git status` - Review what will be committed
2. `git diff --cached` - Verify only your changes
3. `git add <specific-files>` - Stage explicit files
4. `composer lint && composer test:coverage-check` - Quality gates
5. `npm run typecheck` - TypeScript validation

### Commit Message Format
```
<type>(<scope>): <description>

<optional body>
```

**Format Requirements:**
- ✅ **MUST:** Format: `<type>(<scope>): <description>`
- ✅ **MUST:** Scope is required (module or component name)
- ✅ **MUST:** Types: `feat`, `fix`, `docs`, `test`, `refactor`, `style`, `chore`

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `test`: Adding/updating tests
- `refactor`: Code change (no new features/fixes)
- `style`: Formatting, no logic change
- `chore`: Build/tooling/config changes

**Scope Examples:**
- `core`: Core module changes
- `{domainmodule}`: {DomainModule} module changes
- `{servicename}`: {ServiceName} service changes
- `app`: Root application changes
- `config`: Configuration changes
- `plans`: Planning documentation

**Examples:**
```
feat(core): add HTTP client service
fix(wordpress): resolve category parent validation
docs(plans): update {ServiceName} SDK plan
test(lmstudio): add inference service tests
```

### Commit Size Guidelines
- **1-5 files:** Usually appropriate
- **5-15 files:** Acceptable if same feature (Controller + Service + Tests)
- **15-30 files:** Justify in commit message (e.g., new module setup)
- **>30 files:** Consider splitting into multiple commits

### Commit Message Metadata (REQUIRED for AI-generated code)

**For AI-generated commits, metadata is REQUIRED:**

```
<type>(<scope>): <description>

<optional body>

Generated-By: <Tool or Agent Name>
Generated-By-Tool: <Tool Name>
Model: <model-version>
Task-ID: <PREFIX-N> or N/A
Plan: doc/plans/... or N/A
Coverage: XX% or N/A or Documentation
```

**REQUIRED Metadata Fields (for AI-generated code):**
- ✅ **MUST:** `Generated-By` - Tool or agent responsible (e.g., "Cursor Pro", "ChatGPT Plus")
- ✅ **MUST:** `Generated-By-Tool` - Tool name (e.g., "Cursor Pro", "GitHub Copilot")
- ✅ **MUST:** `Model` - Model version (e.g., "Auto", "claude-sonnet-3.5-20241022", "gpt-4-turbo-2024-04-09")
- ✅ **MUST:** `Task-ID` - Task reference (e.g., "SDK-1", "AUTH-2") or `N/A` if no task exists
- ✅ **MUST:** `Plan` - Plan file path (e.g., "doc/plans/technical/2025-11-13-external-sdk-integration.md") or `N/A` if no plan exists
- ✅ **MUST:** `Coverage` - Test coverage percentage (e.g., "95%") or `N/A` if no code changes, or `Documentation` for docs-only commits

**Rules:**
- ✅ **MUST:** All fields present (cannot omit fields)
- ✅ **MUST:** Use `N/A` for Task-ID or Plan if not applicable (do not leave blank)
- ✅ **MUST:** Use `Documentation` for Coverage if commit only changes documentation
- ❌ **FORBIDDEN:** Omitting metadata fields for AI-generated commits
- ❌ **FORBIDDEN:** Leaving Task-ID or Plan blank (must use `N/A`)

**Examples:**
```
feat(core): add HTTP client service

Implement reusable HTTP client wrapper for Guzzle.

Generated-By: Cursor Pro
Generated-By-Tool: Cursor Pro
Model: Auto
Task-ID: CORE-1
Plan: doc/plans/technical/2025-11-14-core-http-client.md
Coverage: 95%
```

```
docs(guides): add Laravel components patterns guide

Generated-By: Cursor Pro
Generated-By-Tool: Cursor Pro
Model: Auto
Task-ID: N/A
Plan: N/A
Coverage: Documentation
```

**Note:** For human commits, metadata is optional but recommended. See [Commit Message Metadata Plan](../plans/technical/2025-11-14-commit-message-metadata.md) for enforcement timeline.

### Decision Rule
"Could this commit be reverted independently without breaking anything?"
- ✅ Yes → Good commit boundary
- ❌ No → Consider combining or splitting

---

## Pre-commit Validation

### Required Checks (MANDATORY)
```bash
# 1. Quality pipeline
composer lint

# 2. Test coverage enforcement
composer test:coverage-check

# 3. TypeScript validation
npm run typecheck

# 4. Build check
npm run build
```

### Enforcement
- Pre-commit hook: Checks for `declare(strict_types=1)` presence
- CI/CD: All 4 checks must pass (no bypass for anyone)
- Build failure: Coverage below thresholds, quality violations, TypeScript errors

---

## Quick Reference Commands

### Development
```bash
composer dev                 # Start full dev stack (server + queue + logs + vite)
composer lint                # Full quality pipeline
composer test:coverage-check # Test with coverage enforcement
npm run typecheck            # TypeScript validation
npm run build               # Production build check
```

### Quality Tools Individual
```bash
composer lint:pint           # Auto-fix style issues
composer lint:phpcs          # PSR-12 validation
composer analyze:phpmd       # Design quality checks
composer analyze:phpstan     # Static analysis (max level)
```

### Module Management
```bash
php artisan module:make {DomainModule}  # Create new module
php artisan migrate                # Run all module migrations
```

### Testing
```bash
composer test                      # Run all tests
composer test -- --filter UserServiceTest  # Specific test
composer test:coverage            # HTML coverage report
```
---

Copyright (c) 2025 Viet Vu  
Company: JOOservices Ltd  
Licensed under the MIT License.
