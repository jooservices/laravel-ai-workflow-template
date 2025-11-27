# Queue Jobs Guide

Complete guide to queue job standards, retry strategies, failed job handling, and monitoring.

> **Related Documentation:**
> - [Laravel Components & Patterns](../guides/laravel-components-patterns.md#queue-system) - Basic job patterns
> - [Job Data Passing Policy](../reference/standards.md#job-data-passing-policy) - Security requirements
> - [Performance by Design](../architecture/principles.md#performance-by-design) - Async processing

---

## Overview

Queue jobs enable **asynchronous processing** of time-consuming tasks without blocking HTTP requests. They improve user experience and application scalability.

**When to use jobs:**
- ✅ Long-running operations (API calls, image processing, reports)
- ✅ External service integration (emails, notifications, third-party APIs)
- ✅ Heavy computation (data analysis, file generation)
- ✅ Scheduled tasks (cleanup, sync operations)

---

## Queue Connection Configuration

### Default Queue Connections

**Environment-based configuration:**

```php
// config/queue.php
'connections' => [
    'sync' => [
        'driver' => 'sync',  // Synchronous (for local development)
    ],
    
    'database' => [
        'driver' => 'database',
        'table' => 'jobs',
        'queue' => 'default',
        'retry_after' => 90,
    ],
    
    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => env('REDIS_QUEUE', 'default'),
        'retry_after' => 90,
        'block_for' => null,
    ],
],
```

### Queue Connection Standards

**Rules:**
- ✅ **MUST:** Use `database` driver for local development (simple setup)
- ✅ **SHOULD:** Use `redis` driver for production (better performance)
- ✅ **MUST:** Configure queue connection per job via config/environment
- ✅ **MUST:** Set appropriate `retry_after` timeout (seconds)

**Example:**
```php
// .env
QUEUE_CONNECTION=database  # Local
QUEUE_CONNECTION=redis     # Production

# Module-specific queue configuration
LMSTUDIO_QUEUE_CONNECTION=redis
LMSTUDIO_QUEUE=lmstudio
```

---

## Job Structure

### Standard Job Template

```php
<?php

declare(strict_types=1);

namespace Modules\Core\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

final class ProcessExampleJob implements ShouldQueue
{
    use Dispatchable;
    use InteractsWithQueue;
    use Queueable;
    // ❌ REMOVE SerializesModels if not passing Model instances

    public function __construct(
        private readonly string $jobUuid
    ) {
        $this->onConnection(config('example.queue_connection', config('queue.default')));
        $this->onQueue(config('example.queue', 'default'));
    }

    public function handle(): void
    {
        // Job logic
    }
}
```

### MANDATORY Requirements

- ✅ **MUST:** Include `declare(strict_types=1);` at top
- ✅ **MUST:** Use `final` keyword for job class
- ✅ **MUST:** Implement `ShouldQueue` interface
- ✅ **MUST:** Use `Dispatchable`, `InteractsWithQueue`, `Queueable` traits
- ✅ **MUST:** Remove `SerializesModels` if not passing Model instances
- ✅ **MUST:** Pass ID/UUID, not Model instances (security requirement)
- ✅ **MUST:** Configure queue connection and queue name in constructor

---

## Queue Configuration in Jobs

### Setting Queue Connection

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function __construct(private readonly string $jobUuid)
    {
        // ✅ Use module-specific config
        $this->onConnection(config('example.queue_connection', config('queue.default')));
        
        // ✅ Use module-specific queue name
        $this->onQueue(config('example.queue', 'default'));
    }
}
```

### Queue Priority

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function __construct(private readonly string $jobUuid)
    {
        // ✅ High priority queue
        $this->onQueue('high');
        
        // ✅ Low priority queue
        $this->onQueue('low');
    }
}
```

### Delayed Jobs

```php
// Dispatch with delay
ProcessExampleJob::dispatch($jobUuid)
    ->delay(now()->addMinutes(5));

// Or in job constructor
final class ProcessExampleJob implements ShouldQueue
{
    public function __construct(private readonly string $jobUuid)
    {
        $this->delay(now()->addMinutes(5));
    }
}
```

---

## Retry Strategies

### Basic Retry Configuration

```php
final class ProcessExampleJob implements ShouldQueue
{
    public int $tries = 3;  // Maximum retry attempts
    public int $timeout = 120;  // Timeout in seconds
    
    public function handle(): void
    {
        // Job logic
    }
}
```

### Exponential Backoff

```php
final class ProcessExampleJob implements ShouldQueue
{
    public int $tries = 5;
    
    public function backoff(): array
    {
        // Retry after 1s, 2s, 4s, 8s, 16s
        return [1, 2, 4, 8, 16];
    }
    
    public function handle(): void
    {
        // Job logic
    }
}
```

### Retry with Conditions

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function handle(): void
    {
        try {
            // Job logic
        } catch (TransientException $e) {
            // Retry on transient errors
            throw $e;
        } catch (PermanentException $e) {
            // Don't retry on permanent errors
            \Log::error('Permanent error in job', ['error' => $e->getMessage()]);
            $this->fail($e);  // Mark as failed without retry
        }
    }
}
```

### Retry Decision Method

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function retryUntil(): \DateTime
    {
        // Retry until 1 hour from now
        return now()->addHour();
    }
    
    public function handle(): void
    {
        // Job logic
    }
}
```

---

## Failed Job Handling

### Failed Method

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function handle(): void
    {
        // Job logic
    }
    
    public function failed(?\Throwable $exception): void
    {
        // Handle job failure
        \Log::error('Job failed permanently', [
            'job_uuid' => $this->jobUuid,
            'exception' => $exception?->getMessage(),
            'trace' => $exception?->getTraceAsString(),
        ]);
        
        // Update database record
        $job = ExampleJob::where('uuid', $this->jobUuid)->first();
        if ($job !== null) {
            $job->update([
                'status' => 'failed',
                'error' => $exception?->getMessage(),
            ]);
        }
    }
}
```

### Graceful Failure

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function handle(): void
    {
        $job = ExampleJob::where('uuid', $this->jobUuid)->first();
        
        if ($job === null) {
            \Log::warning('Job not found', ['uuid' => $this->jobUuid]);
            $this->delete();  // Remove from queue gracefully
            return;
        }
        
        // Check if job should be processed
        if ($job->status === 'cancelled') {
            \Log::info('Job cancelled', ['uuid' => $this->jobUuid]);
            $this->delete();
            return;
        }
        
        // Process job...
    }
}
```

---

## Job Timeouts

### Setting Job Timeout

```php
final class ProcessExampleJob implements ShouldQueue
{
    public int $timeout = 300;  // 5 minutes
    
    public function handle(): void
    {
        // Long-running job logic
    }
}
```

### Timeout Handling

```php
final class ProcessExampleJob implements ShouldQueue
{
    public int $timeout = 120;
    
    public function handle(): void
    {
        // If job exceeds timeout, Laravel will:
        // 1. Mark job as failed
        // 2. Call failed() method if defined
        // 3. Log timeout error
    }
}
```

---

## Job Monitoring

### Logging Job Execution

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function handle(): void
    {
        \Log::info('Job started', ['uuid' => $this->jobUuid]);
        
        try {
            // Job logic
            
            \Log::info('Job completed', ['uuid' => $this->jobUuid]);
        } catch (\Exception $e) {
            \Log::error('Job error', [
                'uuid' => $this->jobUuid,
                'error' => $e->getMessage(),
            ]);
            throw $e;  // Re-throw to trigger retry
        }
    }
}
```

### Tracking Job Progress

```php
final class ProcessExampleJob implements ShouldQueue
{
    public function handle(): void
    {
        $job = ExampleJob::where('uuid', $this->jobUuid)->first();
        
        // Update progress
        $job->update(['progress' => 25]);
        
        // Process first part...
        
        $job->update(['progress' => 50]);
        
        // Process second part...
        
        $job->update(['progress' => 100, 'status' => 'completed']);
    }
}
```

---

## Job Dispatching Patterns

### Immediate Dispatch

```php
// Dispatch immediately
ProcessExampleJob::dispatch($jobUuid);
```

### Delayed Dispatch

```php
// Dispatch after delay
ProcessExampleJob::dispatch($jobUuid)
    ->delay(now()->addMinutes(5));

// Dispatch at specific time
ProcessExampleJob::dispatch($jobUuid)
    ->delay(now()->addHours(1));
```

### Chain Jobs

```php
use Illuminate\Bus\Chain;

// Execute jobs in sequence
Bus::chain([
    new ProcessStepOneJob($jobUuid),
    new ProcessStepTwoJob($jobUuid),
    new ProcessStepThreeJob($jobUuid),
])->dispatch();
```

### Batch Jobs

```php
use Illuminate\Bus\Batch;

// Execute multiple jobs in parallel
$batch = Bus::batch([
    new ProcessJob($uuid1),
    new ProcessJob($uuid2),
    new ProcessJob($uuid3),
])->dispatch();

// Access batch
$batch->id;
$batch->progress();
```

---

## Queue Workers

### Running Queue Workers

```bash
# Default queue
php artisan queue:work

# Specific connection
php artisan queue:work redis

# Specific queue
php artisan queue:work --queue=high,default,low

# With timeout
php artisan queue:work --timeout=120

# Number of tries
php artisan queue:work --tries=3

# Supervisor configuration (recommended for production)
```

### Supervisor Configuration

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /path/to/artisan queue:work redis --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=www-data
numprocs=4
redirect_stderr=true
stdout_logfile=/path/to/worker.log
stopwaitsecs=3600
```

---

## Queue Best Practices

### DO

- ✅ **Pass ID/UUID, not Model instances** (security and data integrity)
- ✅ **Handle missing data gracefully** (return early, don't throw)
- ✅ **Configure queue connection** per job via config
- ✅ **Set appropriate retry limits** based on job type
- ✅ **Use exponential backoff** for retries
- ✅ **Log job execution** for monitoring
- ✅ **Handle failures explicitly** in `failed()` method
- ✅ **Remove `SerializesModels`** if not passing Model instances

### DON'T

- ❌ **Don't pass Model instances** to job constructor
- ❌ **Don't pass sensitive data** (passwords, tokens)
- ❌ **Don't use jobs for fast operations** (use sync execution instead)
- ❌ **Don't ignore job failures** (implement `failed()` method)
- ❌ **Don't create long-running jobs** without timeout handling
- ❌ **Don't use default queue** for all jobs (use priority queues)

---

## Common Patterns

### Email Job

```php
final class SendEmailJob implements ShouldQueue
{
    public function __construct(
        private readonly int $userId,
        private readonly string $emailType,
        private readonly array $data = []
    ) {
        $this->onQueue('emails');
    }
    
    public function handle(): void
    {
        $user = User::find($this->userId);
        if ($user === null) {
            return;
        }
        
        // Send email based on type
        Mail::to($user->email)->send(new $this->emailType($this->data));
    }
    
    public function failed(?\Throwable $exception): void
    {
        \Log::error('Email sending failed', [
            'user_id' => $this->userId,
            'type' => $this->emailType,
            'error' => $exception?->getMessage(),
        ]);
    }
}
```

### API Sync Job

```php
final class SyncExternalApiJob implements ShouldQueue
{
    public int $tries = 3;
    public int $timeout = 120;
    
    public function __construct(private readonly string $resourceUuid)
    {
        $this->onConnection('redis');
        $this->onQueue('api-sync');
    }
    
    public function backoff(): array
    {
        return [30, 60, 120];  // Retry after 30s, 60s, 120s
    }
    
    public function handle(ExternalApiSdkContract $sdk): void
    {
        $resource = Resource::where('uuid', $this->resourceUuid)->first();
        if ($resource === null) {
            return;
        }
        
        try {
            $response = $sdk->sync($resource);
            $resource->update(['synced_at' => now()]);
        } catch (TransientException $e) {
            // Retry on transient errors
            throw $e;
        } catch (PermanentException $e) {
            // Mark as failed on permanent errors
            $this->fail($e);
        }
    }
    
    public function failed(?\Throwable $exception): void
    {
        $resource = Resource::where('uuid', $this->resourceUuid)->first();
        if ($resource !== null) {
            $resource->update([
                'sync_status' => 'failed',
                'sync_error' => $exception?->getMessage(),
            ]);
        }
    }
}
```

---

## Testing Jobs

### Testing Job Dispatch

```php
use Tests\TestCase;
use Illuminate\Support\Facades\Queue;

final class OrderServiceTest extends TestCase
{
    public function test_order_creation_dispatches_email_job(): void
    {
        // Arrange
        Queue::fake();
        
        // Act
        $this->orderService->create([...]);
        
        // Assert
        Queue::assertPushed(SendEmailJob::class, function ($job) {
            return $job->userId === 1;
        });
    }
}
```

### Testing Job Execution

```php
use Tests\TestCase;
use App\Models\User;

final class SendEmailJobTest extends TestCase
{
    public function test_job_sends_email(): void
    {
        // Arrange
        Mail::fake();
        $user = User::factory()->create();
        
        // Act
        SendEmailJob::dispatch($user->id, 'WelcomeEmail');
        
        // Assert
        Mail::assertSent(WelcomeEmail::class, function ($mail) use ($user) {
            return $mail->hasTo($user->email);
        });
    }
}
```

---

## Summary Checklist

### Creating Jobs

- [ ] Include `declare(strict_types=1);`
- [ ] Use `final` keyword
- [ ] Implement `ShouldQueue` interface
- [ ] Pass ID/UUID, not Model instances
- [ ] Remove `SerializesModels` if not needed
- [ ] Configure queue connection and queue name
- [ ] Handle missing data gracefully

### Job Configuration

- [ ] Set appropriate retry limits (`$tries`)
- [ ] Set timeout (`$timeout`)
- [ ] Configure exponential backoff if needed
- [ ] Implement `failed()` method for error handling

### Job Execution

- [ ] Log job execution for monitoring
- [ ] Handle exceptions appropriately
- [ ] Update job status in database
- [ ] Use transactions for database operations

---

> **Related Documentation:**
> - [Laravel Components & Patterns](../guides/laravel-components-patterns.md#queue-system) - Basic job patterns
> - [Job Data Passing Policy](../reference/standards.md#job-data-passing-policy) - Security requirements
> - [Performance by Design](../architecture/principles.md#performance-by-design) - Async processing

