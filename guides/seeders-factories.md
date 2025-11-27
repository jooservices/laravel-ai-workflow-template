# Seeders & Factories Guide

Complete guide to database seeding, factory patterns, and test data generation standards.

> **Related Documentation:**
> - [Database Migrations](../guides/database-migrations.md) - Schema definitions
> - [Testing Patterns](../guides/testing-patterns.md) - Test data generation
> - [Repository Pattern](../architecture/flow.md#3-repository---database-access-only) - Database access layer

---

## Overview

**Seeders** populate the database with initial or test data.  
**Factories** generate fake model instances for testing and seeding.

**When to use:**
- ✅ **Seeders:** Initial data (roles, categories, admin users), development/test data
- ✅ **Factories:** Unit tests, feature tests, generating test data on-the-fly

---

## Factory Standards

### Factory Naming Convention

**Pattern:** `{ModelName}Factory`

**Rules:**
- ✅ **MUST:** Match model name exactly (e.g., `User` → `UserFactory`)
- ✅ **MUST:** Place in `database/factories/` directory
- ✅ **MUST:** Use PascalCase for class name

**Examples:**
```php
// ✅ GOOD
database/factories/UserFactory.php
database/factories/ProductFactory.php
database/factories/CategoryFactory.php

// ❌ WRONG
database/factories/UsersFactory.php        // Plural
database/factories/user_factory.php        // Wrong case
```

### Factory Structure

```php
<?php

declare(strict_types=1);

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Facades\Hash;

/**
 * @extends Factory<User>
 */
final class UserFactory extends Factory
{
    protected $model = User::class;

    public function definition(): array
    {
        return [
            'uuid' => fake()->uuid(),
            'email' => fake()->unique()->safeEmail(),
            'password' => Hash::make('password'),
            'name' => fake()->name(),
            'created_at' => now(),
            'updated_at' => now(),
        ];
    }
}
```

### MANDATORY Requirements

- ✅ **MUST:** Include `declare(strict_types=1);` at top
- ✅ **MUST:** Use `final` keyword for factory class
- ✅ **MUST:** Define `$model` property with model class
- ✅ **MUST:** Implement `definition()` method returning array
- ✅ **MUST:** Use `fake()` helper for random data generation
- ✅ **MUST:** Use `unique()` modifier for unique fields (email, UUID)

---

## Factory Data Generation

### Using Faker

**Common Faker methods:**
```php
public function definition(): array
{
    return [
        // Strings
        'name' => fake()->name(),
        'email' => fake()->email(),
        'text' => fake()->text(),
        'sentence' => fake()->sentence(),
        'paragraph' => fake()->paragraph(),
        
        // Numbers
        'count' => fake()->numberBetween(1, 100),
        'price' => fake()->randomFloat(2, 10, 1000),
        
        // Dates
        'created_at' => fake()->dateTimeBetween('-1 year', 'now'),
        'published_at' => fake()->optional()->dateTimeBetween('-1 month', 'now'),
        
        // Booleans
        'is_active' => fake()->boolean(),
        
        // UUIDs
        'uuid' => fake()->uuid(),
        
        // URLs
        'website' => fake()->url(),
        'image_url' => fake()->imageUrl(),
        
        // Enums/Arrays
        'status' => fake()->randomElement(['active', 'inactive', 'pending']),
        'tags' => fake()->words(3),
    ];
}
```

### Unique Values

**Use `unique()` modifier for unique fields:**
```php
public function definition(): array
{
    return [
        'email' => fake()->unique()->email(),
        'uuid' => fake()->unique()->uuid(),
        'slug' => fake()->unique()->slug(),
    ];
}
```

### Optional Values

**Use `optional()` modifier for nullable fields:**
```php
public function definition(): array
{
    return [
        'phone' => fake()->optional()->phoneNumber(),
        'deleted_at' => fake()->optional()->dateTimeBetween('-1 year', 'now'),
    ];
}
```

---

## Factory States

### Creating Factory States

**States modify factory definitions for specific scenarios:**
```php
final class UserFactory extends Factory
{
    public function definition(): array
    {
        return [
            'email' => fake()->email(),
            'is_active' => true,
            'email_verified_at' => now(),
        ];
    }
    
    // State: Inactive user
    public function inactive(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_active' => false,
        ]);
    }
    
    // State: Unverified email
    public function unverified(): static
    {
        return $this->state(fn (array $attributes) => [
            'email_verified_at' => null,
        ]);
    }
    
    // State: Admin user
    public function admin(): static
    {
        return $this->state(fn (array $attributes) => [
            'role' => 'admin',
        ]);
    }
}
```

### Using Factory States

```php
// Generate with state
User::factory()->inactive()->create();
User::factory()->unverified()->create();
User::factory()->admin()->create();

// Multiple states
User::factory()->inactive()->unverified()->create();
```

---

## Factory Relationships

### HasOne Relationship

```php
final class UserFactory extends Factory
{
    public function definition(): array
    {
        return [
            'email' => fake()->email(),
            'name' => fake()->name(),
        ];
    }
    
    // User has one Profile
    public function withProfile(): static
    {
        return $this->has(Profile::factory(), 'profile');
    }
}
```

### HasMany Relationship

```php
final class CategoryFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->word(),
            'slug' => fake()->slug(),
        ];
    }
    
    // Category has many Products
    public function withProducts(int $count = 3): static
    {
        return $this->has(Product::factory()->count($count), 'products');
    }
}
```

### BelongsTo Relationship

```php
final class ProductFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->sentence(),
            'price' => fake()->randomFloat(2, 10, 1000),
            'category_id' => Category::factory(),
        ];
    }
    
    // Product belongs to Category (using factory)
    public function forCategory(Category $category): static
    {
        return $this->state(fn (array $attributes) => [
            'category_id' => $category->id,
        ]);
    }
}
```

### Many-to-Many Relationships

```php
final class ProductFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->sentence(),
            'price' => fake()->randomFloat(2, 10, 1000),
        ];
    }
    
    // Product belongs to many Tags
    public function withTags(int $count = 3): static
    {
        return $this->afterCreating(function (Product $product) use ($count) {
            $tags = Tag::factory()->count($count)->create();
            $product->tags()->attach($tags);
        });
    }
}
```

---

## Seeder Standards

### Seeder Naming Convention

**Pattern:** `{Name}Seeder`

**Rules:**
- ✅ **MUST:** Use PascalCase for class name
- ✅ **MUST:** Place in `database/seeders/` directory
- ✅ **MUST:** Use descriptive names (e.g., `RoleSeeder`, `AdminUserSeeder`)

**Examples:**
```php
// ✅ GOOD
database/seeders/RoleSeeder.php
database/seeders/AdminUserSeeder.php
database/seeders/ProductCategorySeeder.php

// ❌ WRONG
database/seeders/roles.php                  // Wrong case
database/seeders/AdminSeeder.php            // Too generic
```

### Seeder Structure

```php
<?php

declare(strict_types=1);

namespace Database\Seeders;

use App\Models\Role;
use Illuminate\Database\Seeder;

final class RoleSeeder extends Seeder
{
    public function run(): void
    {
        $roles = [
            ['name' => 'admin', 'display_name' => 'Administrator'],
            ['name' => 'user', 'display_name' => 'User'],
            ['name' => 'moderator', 'display_name' => 'Moderator'],
        ];
        
        foreach ($roles as $role) {
            Role::firstOrCreate(
                ['name' => $role['name']],
                $role
            );
        }
    }
}
```

### MANDATORY Requirements

- ✅ **MUST:** Include `declare(strict_types=1);` at top
- ✅ **MUST:** Use `final` keyword for seeder class
- ✅ **MUST:** Extend `Illuminate\Database\Seeder`
- ✅ **MUST:** Implement `run()` method
- ✅ **MUST:** Use `firstOrCreate()` or `updateOrCreate()` to prevent duplicates

---

## Seeder Patterns

### Idempotent Seeders

**Use `firstOrCreate()` to prevent duplicates:**
```php
final class CategorySeeder extends Seeder
{
    public function run(): void
    {
        $categories = [
            ['name' => 'Electronics', 'slug' => 'electronics'],
            ['name' => 'Clothing', 'slug' => 'clothing'],
            ['name' => 'Books', 'slug' => 'books'],
        ];
        
        foreach ($categories as $category) {
            Category::firstOrCreate(
                ['slug' => $category['slug']],  // Search by unique field
                $category                         // Create with these attributes
            );
        }
    }
}
```

### Conditional Seeders

**Check environment before seeding:**
```php
final class AdminUserSeeder extends Seeder
{
    public function run(): void
    {
        if (app()->environment('production')) {
            return;  // Don't seed admin users in production
        }
        
        User::firstOrCreate(
            ['email' => 'admin@example.com'],
            [
                'name' => 'Admin User',
                'password' => Hash::make('password'),
                'role' => 'admin',
            ]
        );
    }
}
```

### Using Factories in Seeders

```php
final class ProductSeeder extends Seeder
{
    public function run(): void
    {
        // Create 50 products
        Product::factory()->count(50)->create();
        
        // Create products with relationships
        Category::factory()
            ->count(10)
            ->has(Product::factory()->count(5), 'products')
            ->create();
    }
}
```

---

## DatabaseSeeder

### Main Database Seeder

**Orchestrate all seeders:**
```php
<?php

declare(strict_types=1);

namespace Database\Seeders;

use Illuminate\Database\Seeder;

final class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // Seed in order (respect dependencies)
        $this->call([
            RoleSeeder::class,
            AdminUserSeeder::class,
            CategorySeeder::class,
            ProductSeeder::class,
        ]);
    }
}
```

### Running Seeders

```bash
# Run all seeders
php artisan db:seed

# Run specific seeder
php artisan db:seed --class=RoleSeeder

# Run with fresh migration
php artisan migrate:fresh --seed
```

---

## Module Seeders

### Module Seeder Location

**Path:** `Modules/{ModuleName}/Database/Seeders/`

**Example:**
```
Modules/Product/Database/Seeders/
├── ProductCategorySeeder.php
├── ProductSeeder.php
└── ProductTagSeeder.php
```

### Module Seeder Structure

```php
<?php

declare(strict_types=1);

namespace Modules\Product\Database\Seeders;

use Illuminate\Database\Seeder;
use Modules\Product\Models\Category;

final class ProductCategorySeeder extends Seeder
{
    public function run(): void
    {
        $categories = [
            ['name' => 'Electronics', 'slug' => 'electronics'],
            ['name' => 'Clothing', 'slug' => 'clothing'],
        ];
        
        foreach ($categories as $category) {
            Category::firstOrCreate(
                ['slug' => $category['slug']],
                $category
            );
        }
    }
}
```

---

## Testing with Factories

### Unit Tests

```php
use Tests\TestCase;
use App\Models\User;

final class UserServiceTest extends TestCase
{
    public function test_user_creation(): void
    {
        // Arrange
        $user = User::factory()->create();
        
        // Act
        $result = $this->service->getUser($user->id);
        
        // Assert
        $this->assertEquals($user->id, $result->id);
    }
}
```

### Feature Tests

```php
use Tests\TestCase;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

final class UserApiTest extends TestCase
{
    use RefreshDatabase;
    
    public function test_user_can_view_profile(): void
    {
        // Arrange
        $user = User::factory()->create();
        
        // Act
        $response = $this->actingAs($user)->get('/api/v1/profile');
        
        // Assert
        $response->assertOk();
    }
}
```

### Creating Multiple Instances

```php
// Create 10 users
$users = User::factory()->count(10)->create();

// Create with states
$inactiveUsers = User::factory()->count(5)->inactive()->create();

// Create with relationships
$categories = Category::factory()
    ->count(5)
    ->has(Product::factory()->count(10), 'products')
    ->create();
```

---

## Best Practices

### DO

- ✅ **Use factories for test data** (not seeders)
- ✅ **Make seeders idempotent** (use `firstOrCreate()`)
- ✅ **Use descriptive factory states** for common scenarios
- ✅ **Create relationships** using factory methods
- ✅ **Use `fake()` helper** for random data generation
- ✅ **Use `unique()` modifier** for unique fields

### DON'T

- ❌ **Don't use seeders in tests** (use factories directly)
- ❌ **Don't create seeders** that depend on random data
- ❌ **Don't hardcode IDs** in seeders (use relationships)
- ❌ **Don't seed production data** via seeders (use migrations or scripts)
- ❌ **Don't create factories** for every model (only if needed for testing)

---

## Common Patterns

### Creating Admin User

```php
final class AdminUserSeeder extends Seeder
{
    public function run(): void
    {
        User::firstOrCreate(
            ['email' => 'admin@example.com'],
            [
                'name' => 'Admin User',
                'password' => Hash::make('password'),
                'role' => 'admin',
                'email_verified_at' => now(),
            ]
        );
    }
}
```

### Seeding Reference Data

```php
final class CountrySeeder extends Seeder
{
    public function run(): void
    {
        $countries = [
            ['code' => 'US', 'name' => 'United States'],
            ['code' => 'VN', 'name' => 'Vietnam'],
            ['code' => 'UK', 'name' => 'United Kingdom'],
        ];
        
        foreach ($countries as $country) {
            Country::firstOrCreate(
                ['code' => $country['code']],
                $country
            );
        }
    }
}
```

### Creating Test Data with Relationships

```php
final class TestDataSeeder extends Seeder
{
    public function run(): void
    {
        // Create categories with products
        Category::factory()
            ->count(5)
            ->has(Product::factory()->count(10), 'products')
            ->create();
        
        // Create users with orders
        User::factory()
            ->count(10)
            ->has(Order::factory()->count(3), 'orders')
            ->create();
    }
}
```

---

## Summary Checklist

### Creating Factories

- [ ] Use correct naming convention (`{ModelName}Factory`)
- [ ] Include `declare(strict_types=1);`
- [ ] Use `final` keyword
- [ ] Define `$model` property
- [ ] Implement `definition()` method
- [ ] Use `fake()` helper for random data
- [ ] Use `unique()` for unique fields

### Creating Seeders

- [ ] Use correct naming convention (`{Name}Seeder`)
- [ ] Include `declare(strict_types=1);`
- [ ] Use `final` keyword
- [ ] Make idempotent (use `firstOrCreate()`)
- [ ] Check environment if needed
- [ ] Document dependencies

### Using in Tests

- [ ] Use factories for test data generation
- [ ] Use states for specific scenarios
- [ ] Create relationships using factory methods
- [ ] Avoid hardcoding test data

---

> **Related Documentation:**
> - [Database Migrations](../guides/database-migrations.md) - Schema definitions
> - [Testing Patterns](../guides/testing-patterns.md) - Test data generation
> - [Repository Pattern](../architecture/flow.md#3-repository---database-access-only) - Database access layer

