# Module Creation Workflow Guide

Step-by-step guide to creating new modules following the modular architecture principles.

> **Related Documentation:**
> - [Modular Architecture Principles](../architecture/principles.md#5-modular-architecture) - Core vs Domain modules
> - [Module Standards](../reference/standards.md#module-standards) - Naming and structure requirements
> - [Development Guidelines](../development/guidelines.md) - Implementation workflows

---

## Overview

Modules organize code by **business domain**, not technical layer. Each module is self-contained with its own controllers, services, models, routes, and tests.

**Decision Rule:**
- **Business logic?** → Domain module ({DomainModule1}, {DomainModule2}, {DomainModule3})
- **Generic tool?** → Core module (HTTP client, logging, SDKs)

---

## Step 1: Decide Module Placement

### Is it Business Logic?

**YES** → Create domain module:
- {ExternalService} integration → `Modules/{DomainModule}/`
- {BusinessDomain1} features → `Modules/{DomainModule}/`
- {BusinessDomain2} management → `Modules/{DomainModule}/`

**NO** → Continue to next question

### Is it a Generic Tool/Service?

**YES** → Add to Core module:
- HTTP client → `Modules/Core/Services/Http/`
- Logging utilities → `Modules/Core/Logging/`
- SDKs → `Modules/Core/Services/{ServiceName}/`

**NO** → Create new domain module

---

## Step 2: Create Module Structure

### Using Artisan Command

```bash
php artisan module:make {DomainName}
```

**Example:**
```bash
php artisan module:make {DomainModule}
```

This creates the basic module structure:
```
Modules/{DomainModule}/
├── app/
├── config/
├── database/
├── resources/
├── routes/
└── module.json
```

---

## Step 3: Configure Module

### Update `module.json`

```json
{
    "name": "{DomainModule}",
    "alias": "{domainmodule}",
    "description": "{BusinessDomain} management module",
    "keywords": [],
    "priority": 0,
    "providers": [
        "Modules\\{DomainModule}\\Providers\\{DomainModule}ServiceProvider"
    ],
    "aliases": {},
    "files": []
}
```

### Activate Module

Update `modules_statuses.json`:
```json
{
    "Core": true,
    "{DomainModule1}": true,
    "{DomainModule}": true
}
```

---

## Step 4: Create Module Structure

### Required Directories

```
Modules/{DomainModule}/
├── app/
│   ├── Http/
│   │   ├── Controllers/          # API controllers (final classes)
│   │   └── Requests/              # FormRequest validators
│   ├── Models/                    # Domain models (final classes)
│   └── Services/                  # Business logic (final classes)
│       └── Contracts/              # Service interfaces
├── config/
│   └── config.php                 # Module configuration
├── database/
│   ├── migrations/                # Database migrations
│   └── seeders/                   # Database seeders
├── routes/
│   ├── api.php                    # API routes
│   └── web.php                    # Web routes
├── resources/
│   ├── views/                     # Blade views (if needed)
│   ├── js/                        # Vue components (if needed)
│   └── scss/                      # Styles (if needed)
├── tests/
│   ├── Feature/                   # Feature tests
│   └── Unit/                      # Unit tests
└── module.json                    # Module metadata
```

---

## Step 5: Create Service Provider

### Service Provider Template

```php
<?php

declare(strict_types=1);

namespace Modules\{DomainModule}\Providers;

use Illuminate\Support\ServiceProvider;

final class {DomainModule}ServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        // Register service bindings
        $this->app->bind(
            \Modules\{DomainModule}\Services\Contracts\{DomainModule}ServiceContract::class,
            \Modules\{DomainModule}\Services\{DomainModule}Service::class
        );
    }

    public function boot(): void
    {
        // Load migrations
        $this->loadMigrationsFrom(__DIR__ . '/../database/migrations');
        
        // Load routes
        $this->loadRoutesFrom(__DIR__ . '/../routes/api.php');
        $this->loadRoutesFrom(__DIR__ . '/../routes/web.php');
        
        // Load config
        $this->mergeConfigFrom(
            __DIR__ . '/../config/config.php',
            '{domainmodule}'
        );
    }
}
```

---

## Step 6: Create Routes

### API Routes (`routes/api.php`)

```php
<?php

declare(strict_types=1);

use Illuminate\Support\Facades\Route;
use Modules\{DomainModule}\Http\Controllers\{DomainModule}Controller;

Route::middleware(['auth:sanctum'])->group(function () {
    Route::get('/{resources}', [{DomainModule}Controller::class, 'index']);
    Route::post('/{resources}', [{DomainModule}Controller::class, 'store']);
    Route::get('/{resources}/{uuid}', [{DomainModule}Controller::class, 'show']);
    Route::put('/{resources}/{uuid}', [{DomainModule}Controller::class, 'update']);
    Route::delete('/{resources}/{uuid}', [{DomainModule}Controller::class, 'destroy']);
});
```

**Note:** Routes are automatically prefixed with `/api/v1` by Laravel Modules.

---

## Step 7: Create Service Layer

### Service Contract

```php
<?php

declare(strict_types=1);

namespace Modules\{DomainModule}\Services\Contracts;

use Modules\{DomainModule}\Models\{Entity};

interface {DomainModule}ServiceContract
{
    public function list(array $filters = []): array;
    
    public function create(array $data): {Entity};
    
    public function update({Entity} ${entity}, array $data): {Entity};
    
    public function delete({Entity} ${entity}): void;
}
```

### Service Implementation

```php
<?php

declare(strict_types=1);

namespace Modules\{DomainModule}\Services;

use App\Logging\ActionLogger;
use Modules\{DomainModule}\Models\{Entity};
use Modules\{DomainModule}\Repositories\Contracts\{DomainModule}RepositoryContract;
use Modules\{DomainModule}\Services\Contracts\{DomainModule}ServiceContract;

final class {DomainModule}Service implements {DomainModule}ServiceContract
{
    public function __construct(
        private readonly {DomainModule}RepositoryContract $repository,
        private readonly ActionLogger $logger,
    ) {}

    public function list(array $filters = []): array
    {
        return $this->repository->all($filters);
    }

    public function create(array $data): {Entity}
    {
        ${entity} = $this->repository->create($data);
        
        $this->logger->log(
            operation: '{entity}.created',
            actor: auth()->user(),
            before: [],
            after: ${entity}->toArray(),
        );
        
        return ${entity};
    }

    public function update({Entity} ${entity}, array $data): {Entity}
    {
        $before = ${entity}->toArray();
        
        ${entity} = $this->repository->update(${entity}, $data);
        
        $this->logger->log(
            operation: '{entity}.updated',
            actor: auth()->user(),
            before: $before,
            after: ${entity}->toArray(),
        );
        
        return ${entity};
    }

    public function delete({Entity} ${entity}): void
    {
        $before = ${entity}->toArray();
        
        $this->repository->delete(${entity});
        
        $this->logger->log(
            operation: '{entity}.deleted',
            actor: auth()->user(),
            before: $before,
            after: [],
        );
    }
}
```

---

## Step 8: Create Controller

### Controller Template

```php
<?php

declare(strict_types=1);

namespace Modules\{DomainModule}\Http\Controllers;

use App\Http\Responses\ApiResponse;
use Illuminate\Http\JsonResponse;
use Illuminate\Routing\Controller;
use Modules\{DomainModule}\Http\Requests\Store{Entity}Request;
use Modules\{DomainModule}\Http\Resources\{Entity}Resource;
use Modules\{DomainModule}\Services\Contracts\{DomainModule}ServiceContract;

final class {DomainModule}Controller extends Controller
{
    public function __construct(
        private readonly ProductServiceContract $service,
    ) {}

    public function index(): JsonResponse
    {
        $products = $this->service->list();
        
        return ApiResponse::success(
            code: 'product.list',
            message: 'Products retrieved successfully',
            data: ['items' => ProductResource::collection($products)],
        );
    }

    public function store(StoreProductRequest $request): JsonResponse
    {
        $product = $this->service->create($request->validated());
        
        return ProductResource::make($product)
            ->response()
            ->setStatusCode(201);
    }
}
```

---

## Step 9: Create FormRequest

### FormRequest Template

```php
<?php

declare(strict_types=1);

namespace Modules\{DomainModule}\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

final class Store{Entity}Request extends FormRequest
{
    /**
     * @return array<string, array<int, string|\Illuminate\Contracts\Validation\ValidationRule>>
     */
    public function rules(): array
    {
        return [
            'name' => ['required', 'string', 'max:255'],
            'description' => ['nullable', 'string'],
            'price' => ['required', 'numeric', 'min:0'],
            'category_id' => ['nullable', 'integer', 'exists:categories,id'],
        ];
    }
}
```

**Coverage Requirement:** 100% test coverage for FormRequests.

---

## Step 10: Create Model

### Model Template

```php
<?php

declare(strict_types=1);

namespace Modules\{DomainModule}\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

final class {Entity} extends Model
{
    protected $primaryKey = 'uuid';
    protected $keyType = 'string';
    public $incrementing = false;

    protected $fillable = [
        'uuid',
        'name',
        'description',
        'price',
        'category_uuid',
    ];

    protected $casts = [
        'price' => 'decimal:2',
        'uuid' => 'string',
        'category_uuid' => 'string',
    ];

    protected static function boot(): void
    {
        parent::boot();
        
        static::creating(function ({Entity} ${entity}): void {
            if (empty(${entity}->uuid)) {
                ${entity}->uuid = (string) \Illuminate\Support\Str::uuid();
            }
        });
    }

    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class, 'category_uuid', 'uuid');
    }
}
```

---

## Step 11: Create Migration

### Migration Template

```php
<?php

declare(strict_types=1);

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('{entities}', function (Blueprint $table) {
            $table->uuid('uuid')->primary();
            $table->string('name');
            $table->text('description')->nullable();
            $table->decimal('price', 10, 2);
            $table->foreignUuid('category_uuid')->nullable()->constrained('categories', 'uuid')->nullOnDelete();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('{entities}');
    }
};
```

---

## Step 12: Create Tests

### Unit Test Template

```php
<?php

declare(strict_types=1);

namespace Tests\Unit\Services;

use App\Logging\ActionLogger;
use Modules\{DomainModule}\Services\{DomainModule}Service;
use Modules\{DomainModule}\Repositories\Contracts\{DomainModule}RepositoryContract;
use Tests\TestCase;
use Mockery;

final class {DomainModule}ServiceTest extends TestCase
{
    public function test_create_returns_product(): void
    {
        $repository = Mockery::mock(ProductRepositoryContract::class);
        $logger = Mockery::mock(ActionLogger::class);
        
        $service = new ProductService($repository, $logger);
        
        // Test implementation
    }
}
```

### Feature Test Template

```php
<?php

declare(strict_types=1);

namespace Tests\Feature\Product;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

final class ProductApiTest extends TestCase
{
    use RefreshDatabase;

    public function test_store_creates_product(): void
    {
        $response = $this->postJson('/api/v1/products', [
            'name' => 'Test Product',
            'price' => 99.99,
        ]);
        
        $response->assertStatus(201);
    }
}
```

---

## Step 13: Create Resource (Optional)

### Resource Template

```php
<?php

declare(strict_types=1);

namespace Modules\Product\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

final class ProductResource extends JsonResource
{
    /**
     * @return array<string, mixed>
     */
    public function toArray(Request $request): array
    {
        return [
            'uuid' => $this->uuid,
            'name' => $this->name,
            'description' => $this->description,
            'price' => $this->price,
            'category' => $this->whenLoaded('category'),
        ];
    }
}
```

---

## Module Checklist

Before considering a module complete:

- [ ] Module created with `php artisan module:make`
- [ ] `module.json` configured correctly
- [ ] Module activated in `modules_statuses.json`
- [ ] Service Provider created and registered
- [ ] Routes defined in `routes/api.php` and/or `routes/web.php`
- [ ] Service contract and implementation created
- [ ] Controller created (final class)
- [ ] FormRequest validators created (100% test coverage)
- [ ] Models created (final classes)
- [ ] Migrations created
- [ ] Unit tests written (95% coverage for services)
- [ ] Feature tests written (90% coverage for controllers)
- [ ] Resources created (if using Model-based responses)
- [ ] Configuration file created (if needed)
- [ ] README.md created (module documentation)

---

## Common Patterns

### Module with External API Integration

```php
// Service uses SDK contract
final class ProductService
{
    public function __construct(
        private readonly ProductRepositoryContract $repository,
        private readonly ExternalSdkContract $sdk,  // External API SDK
        private readonly ActionLogger $logger,
    ) {}
}
```

### Module with Repository

```php
// Service uses repository for database access
final class ProductService
{
    public function __construct(
        private readonly ProductRepositoryContract $repository,
        private readonly ActionLogger $logger,
    ) {}
}
```

---

## Related Documentation

- [Modular Architecture Principles](../architecture/principles.md#5-modular-architecture) - Core vs Domain decision
- [Module Standards](../reference/standards.md#module-standards) - Naming and structure
- [Service Layer Flow](../architecture/flow.md) - Controller → Service → Repository/SDK


---

**Copyright (c) 2025 Viet Vu <jooservices@gmail.com>**
**Company: JOOservices Ltd**  
Licensed under the MIT License.
