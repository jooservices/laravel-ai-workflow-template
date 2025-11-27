# Database Transactions Guide

Complete guide to database transaction standards, boundaries, rollback handling, and best practices.

> **Related Documentation:**
> - [Repository Pattern](../architecture/flow.md#3-repository---database-access-only) - Database access layer
> - [Service Layer](../architecture/flow.md#4-service---business-logic) - Business logic layer
> - [Error Handling](../guides/error-handling.md) - Exception handling patterns

---

## Overview

Database transactions ensure **data integrity** by grouping multiple database operations into a single atomic unit. Either all operations succeed, or all are rolled back.

**When to use transactions:**
- ✅ Multiple related database operations that must succeed or fail together
- ✅ Operations that depend on each other (e.g., create parent + child records)
- ✅ Data migrations that transform existing data
- ✅ Complex business logic with multiple database writes

---

## Transaction Basics

### Basic Transaction Pattern

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function () {
    // Multiple database operations
    $user = User::create([...]);
    $profile = Profile::create(['user_id' => $user->id]);
    
    // If any operation fails, all are rolled back automatically
});
```

### How Transactions Work

1. **Start:** `DB::transaction()` begins a transaction
2. **Execute:** All database operations run inside the closure
3. **Commit:** If closure completes successfully, changes are committed
4. **Rollback:** If an exception is thrown, all changes are rolled back automatically

---

## Transaction Boundaries

### Where to Place Transactions

**✅ Service Layer (RECOMMENDED):**
```php
final class OrderService
{
    public function createOrder(array $data): Order
    {
        return DB::transaction(function () use ($data) {
            $order = $this->orderRepository->create($data);
            $this->orderItemRepository->createMany($data['items']);
            $this->inventoryService->deductStock($data['items']);
            
            return $order;
        });
    }
}
```

**✅ Controller (For cross-service operations):**
```php
final class OrderController extends Controller
{
    public function store(StoreOrderRequest $request): JsonResponse
    {
        $order = DB::transaction(function () use ($request) {
            $order = $this->orderService->create($request->validated());
            $this->paymentService->charge($order);
            $this->notificationService->sendConfirmation($order);
            
            return $order;
        });
        
        return OrderResource::make($order)->response();
    }
}
```

**❌ Repository (AVOID):**
```php
// ❌ WRONG: Transactions belong in Service, not Repository
final class OrderRepository
{
    public function createWithItems(array $orderData, array $items): Order
    {
        return DB::transaction(function () {
            // Business logic in Repository - WRONG
        });
    }
}
```

### Decision Matrix

| Scenario | Transaction Location | Rationale |
|----------|---------------------|-----------|
| Single model create/update | No transaction needed | Single operation is already atomic |
| Multiple related models | Service layer | Business logic coordination |
| Cross-service operations | Controller | Orchestrating multiple services |
| Data migration | Migration class | Schema/data transformation |
| Repository query building | No transaction | Query optimization only |

---

## Transaction Best Practices

### ✅ DO

**1. Use transactions for related operations:**
```php
// ✅ GOOD: Related operations in transaction
DB::transaction(function () {
    $order = Order::create([...]);
    OrderItem::createMany([...]);
    Inventory::decrement('stock', $quantity);
});
```

**2. Keep transactions short:**
```php
// ✅ GOOD: Short transaction scope
public function transfer(Account $from, Account $to, int $amount): void
{
    DB::transaction(function () use ($from, $to, $amount) {
        $from->decrement('balance', $amount);
        $to->increment('balance', $amount);
    });
}
```

**3. Handle exceptions explicitly:**
```php
// ✅ GOOD: Explicit exception handling
try {
    DB::transaction(function () {
        // Operations
    });
} catch (\Exception $e) {
    \Log::error('Transaction failed', ['error' => $e->getMessage()]);
    throw new \RuntimeException('Failed to complete operation');
}
```

**4. Return values from transactions:**
```php
// ✅ GOOD: Return transaction result
$order = DB::transaction(function () {
    return Order::create([...]);
});
```

### ❌ DON'T

**1. Don't nest transactions unnecessarily:**
```php
// ❌ BAD: Unnecessary nesting
DB::transaction(function () {
    DB::transaction(function () {
        // Single operation doesn't need nested transaction
        Order::create([...]);
    });
});
```

**2. Don't use transactions for read-only operations:**
```php
// ❌ BAD: No need for transaction on reads
DB::transaction(function () {
    $orders = Order::all();  // Reads don't need transactions
});
```

**3. Don't perform external API calls inside transactions:**
```php
// ❌ BAD: External calls can cause long transactions
DB::transaction(function () {
    $order = Order::create([...]);
    $this->paymentService->charge($order);  // External API call - BAD
    $order->update(['status' => 'paid']);
});

// ✅ GOOD: Separate transaction from external calls
$order = DB::transaction(function () {
    return Order::create([...]);
});
$this->paymentService->charge($order);  // External call outside transaction
$order->update(['status' => 'paid']);
```

**4. Don't ignore transaction failures:**
```php
// ❌ BAD: Silently ignoring failures
try {
    DB::transaction(function () {
        // Operations
    });
} catch (\Exception $e) {
    // Silent failure - BAD
}

// ✅ GOOD: Log and rethrow
try {
    DB::transaction(function () {
        // Operations
    });
} catch (\Exception $e) {
    \Log::error('Transaction failed', ['error' => $e->getMessage()]);
    throw $e;  // Rethrow to caller
}
```

---

## Rollback Handling

### Automatic Rollback

**Laravel automatically rolls back on exceptions:**
```php
DB::transaction(function () {
    $user = User::create([...]);
    Profile::create(['user_id' => $user->id]);  // If this fails...
    // ... entire transaction is rolled back automatically
});
```

### Manual Rollback

**Use `DB::rollBack()` for explicit rollback:**
```php
DB::beginTransaction();
try {
    $user = User::create([...]);
    
    if ($someCondition) {
        DB::rollBack();  // Explicit rollback
        return null;
    }
    
    Profile::create(['user_id' => $user->id]);
    DB::commit();
} catch (\Exception $e) {
    DB::rollBack();  // Rollback on error
    throw $e;
}
```

### Transaction State Checking

```php
DB::beginTransaction();
try {
    // Operations
    
    if (DB::transactionLevel() > 0) {
        // Still inside transaction
        DB::commit();
    }
} catch (\Exception $e) {
    if (DB::transactionLevel() > 0) {
        DB::rollBack();
    }
    throw $e;
}
```

---

## Nested Transactions

### How Nested Transactions Work

**Laravel supports nested transactions using savepoints:**
```php
DB::transaction(function () {
    // Outer transaction
    
    User::create([...]);
    
    DB::transaction(function () {
        // Inner transaction (savepoint)
        Profile::create([...]);
        
        // If this fails, only inner transaction rolls back
        // Outer transaction continues
    });
    
    // If outer transaction fails, everything rolls back
});
```

### Nested Transaction Best Practices

**Use nested transactions for optional operations:**
```php
DB::transaction(function () {
    $order = Order::create([...]);
    
    // Optional: Create notification (can fail without affecting order)
    try {
        DB::transaction(function () {
            Notification::create([...]);
        });
    } catch (\Exception $e) {
        \Log::warning('Notification creation failed', ['error' => $e->getMessage()]);
        // Order still created successfully
    }
});
```

---

## Deadlock Handling

### What Are Deadlocks?

**Deadlocks occur when two transactions wait for each other to release locks:**
```
Transaction 1: Locks Row A, waits for Row B
Transaction 2: Locks Row B, waits for Row A
→ Deadlock!
```

### Handling Deadlocks

**Retry logic for deadlock exceptions:**
```php
use Illuminate\Database\QueryException;

$maxRetries = 3;
$attempt = 0;

while ($attempt < $maxRetries) {
    try {
        return DB::transaction(function () {
            // Operations that might deadlock
        });
    } catch (QueryException $e) {
        if ($e->getCode() === '40001' || str_contains($e->getMessage(), 'Deadlock')) {
            $attempt++;
            
            if ($attempt >= $maxRetries) {
                throw new \RuntimeException('Transaction failed after retries');
            }
            
            // Wait before retry (exponential backoff)
            usleep(100000 * $attempt);  // 100ms, 200ms, 300ms
            
            continue;
        }
        
        throw $e;  // Re-throw non-deadlock exceptions
    }
}
```

### Preventing Deadlocks

**Order locks consistently:**
```php
// ✅ GOOD: Consistent lock order
DB::transaction(function () {
    $user = User::lockForUpdate()->find($userId);
    $order = Order::lockForUpdate()->find($orderId);
    // Always lock User before Order
});

// ❌ BAD: Inconsistent lock order (causes deadlocks)
// Transaction 1: Lock User, then Order
// Transaction 2: Lock Order, then User
// → Potential deadlock!
```

---

## Transaction Timeouts

### Setting Transaction Timeout

**Database-level timeout (MySQL):**
```sql
SET innodb_lock_wait_timeout = 50;  -- 50 seconds
```

**Application-level timeout (PHP):**
```php
DB::transaction(function () {
    set_time_limit(60);  // 60 seconds max execution time
    
    // Long-running operations
}, 5);  // Retry up to 5 times on deadlock
```

---

## Common Patterns

### Create with Related Records

```php
final class OrderService
{
    public function createOrder(array $data): Order
    {
        return DB::transaction(function () use ($data) {
            $order = $this->orderRepository->create([
                'user_id' => $data['user_id'],
                'total' => $data['total'],
            ]);
            
            foreach ($data['items'] as $item) {
                $this->orderItemRepository->create([
                    'order_id' => $order->id,
                    'product_id' => $item['product_id'],
                    'quantity' => $item['quantity'],
                ]);
            }
            
            return $order;
        });
    }
}
```

### Transfer Between Records

```php
final class AccountService
{
    public function transfer(Account $from, Account $to, int $amount): void
    {
        DB::transaction(function () use ($from, $to, $amount) {
            $from->decrement('balance', $amount);
            $to->increment('balance', $amount);
            
            Transaction::create([
                'from_account_id' => $from->id,
                'to_account_id' => $to->id,
                'amount' => $amount,
            ]);
        });
    }
}
```

### Conditional Transaction

```php
final class ProductService
{
    public function updateStock(Product $product, int $quantity): void
    {
        // Only use transaction if multiple operations
        if ($quantity < 0 && $product->stock < abs($quantity)) {
            throw new \InvalidArgumentException('Insufficient stock');
        }
        
        DB::transaction(function () use ($product, $quantity) {
            $product->increment('stock', $quantity);
            
            StockHistory::create([
                'product_id' => $product->id,
                'quantity' => $quantity,
                'reason' => 'manual_adjustment',
            ]);
        });
    }
}
```

---

## Service Layer Integration

### Transaction in Service Methods

```php
final class UserService
{
    public function __construct(
        private readonly UserRepository $userRepository,
        private readonly ProfileRepository $profileRepository,
    ) {}
    
    public function createUser(array $data): User
    {
        return DB::transaction(function () use ($data) {
            $user = $this->userRepository->create([
                'email' => $data['email'],
                'password' => Hash::make($data['password']),
            ]);
            
            $this->profileRepository->create([
                'user_id' => $user->id,
                'first_name' => $data['first_name'],
                'last_name' => $data['last_name'],
            ]);
            
            return $user;
        });
    }
}
```

---

## Error Handling in Transactions

### Exception Handling

```php
try {
    $result = DB::transaction(function () {
        // Operations
        return $value;
    });
} catch (\InvalidArgumentException $e) {
    // Business logic error - don't retry
    \Log::warning('Validation failed', ['error' => $e->getMessage()]);
    throw $e;
} catch (\Exception $e) {
    // Unexpected error - log and rethrow
    \Log::error('Transaction failed', [
        'error' => $e->getMessage(),
        'trace' => $e->getTraceAsString(),
    ]);
    throw new \RuntimeException('Failed to complete operation', 0, $e);
}
```

---

## Testing Transactions

### Testing Transaction Rollback

```php
use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

final class OrderServiceTest extends TestCase
{
    use RefreshDatabase;
    
    public function test_order_creation_rolls_back_on_failure(): void
    {
        // Arrange
        $this->expectException(\Exception::class);
        
        // Act
        try {
            DB::transaction(function () {
                Order::create([...]);
                throw new \Exception('Simulated failure');
            });
        } catch (\Exception $e) {
            // Assert
            $this->assertDatabaseMissing('orders', [
                // Order should not exist due to rollback
            ]);
            
            throw $e;
        }
    }
}
```

---

## Summary Checklist

### Before Using Transactions

- [ ] Identify related database operations that must succeed together
- [ ] Determine transaction boundary (Service vs Controller)
- [ ] Plan exception handling strategy
- [ ] Consider deadlock scenarios

### Implementing Transactions

- [ ] Use `DB::transaction()` closure pattern
- [ ] Keep transactions short (avoid external API calls)
- [ ] Handle exceptions explicitly
- [ ] Return values from transactions when needed
- [ ] Test rollback scenarios

### Best Practices

- [ ] Use transactions in Service layer for business logic
- [ ] Avoid transactions for read-only operations
- [ ] Don't nest transactions unnecessarily
- [ ] Handle deadlocks with retry logic
- [ ] Order locks consistently to prevent deadlocks

---

> **Related Documentation:**
> - [Repository Pattern](../architecture/flow.md#3-repository---database-access-only) - Database access layer
> - [Service Layer](../architecture/flow.md#4-service---business-logic) - Business logic layer
> - [Error Handling](../guides/error-handling.md) - Exception handling patterns

