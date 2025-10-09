# Service Refactoring Guide

## Overview

This guide explains the new service architecture that addresses MongoDB transaction management and large service file issues.

## New Components

### 1. TransactionManager

Handles MongoDB transactions for both global and project databases.

#### Basic Usage

```php
use App\Services\Database\TransactionManager;

$transactionManager = new TransactionManager();

// Global database transaction
$result = $transactionManager->executeInGlobalTransaction(function () {
    // Your global database operations
    return $this->userRepository->save($userData);
});

// Project database transaction
$result = $transactionManager->executeInProjectTransaction(function () {
    // Your project database operations
    return $this->workOrderRepository->save($workOrderData);
});

// Cross-database transaction
$result = $transactionManager->executeInCrossDbTransaction(function () {
    // Operations that span both databases
    $user = $this->globalUserRepository->save($userData);
    $project = $this->projectRepository->save($projectData);
    return ['user' => $user, 'project' => $project];
});
```

#### Manual Transaction Control

```php
$transactionManager = new TransactionManager();

try {
    $transactionManager->startCrossDbTransaction();

    // Your operations here
    $user = $this->globalUserRepository->save($userData);
    $project = $this->projectRepository->save($projectData);

    $transactionManager->commitAll();
} catch (\Exception $e) {
    $transactionManager->abortAll();
    throw $e;
}
```

### 2. ConnectionManager

Efficiently manages database connections and switching.

#### Usage

```php
use App\Services\Database\ConnectionManager;

// Switch to a specific project database
ConnectionManager::setProjectDatabase('project_db_name');

// Execute with temporary database switch
$result = ConnectionManager::withProjectDatabase('temp_db', function () {
    return $this->repository->getAll();
});

// Execute with global database
$result = ConnectionManager::withGlobalDatabase(function () {
    return $this->globalRepository->getAll();
});

// Validate database exists
if (ConnectionManager::validateProjectDatabase('project_db')) {
    // Database is accessible
}
```

### 3. Enhanced Base Services

Updated base services now support cross-database operations.

#### In Global Services (extends App\Services\V2\Globals\BaseService)

```php
class UserService extends BaseService
{
    public function createUserWithProject($userData, $projectData, $dbName)
    {
        return $this->executeWithCrossDbTransaction(function () use ($userData, $projectData, $dbName) {
            // Save to global database
            $user = $this->userRepository->save($userData);

            // Save to project database
            return $this->executeWithProjectDatabase($dbName, function () use ($projectData, $user) {
                $projectData['user_id'] = $user['_id'];
                return $this->projectUserRepository->save($projectData);
            });
        });
    }
}
```

#### In Project Services (extends App\Services\V3\Projects\BaseService)

```php
class WorkOrderService extends BaseService
{
    public function createWorkOrderWithGlobalUpdate($workOrderData, $globalData)
    {
        return $this->executeWithCrossDbTransaction(function () use ($workOrderData, $globalData) {
            // Save to project database
            $workOrder = $this->workOrderRepository->save($workOrderData);

            // Update global database
            return $this->executeWithGlobalDatabase(function () use ($globalData, $workOrder) {
                $globalData['work_order_id'] = $workOrder['_id'];
                return $this->globalRepository->save($globalData);
            });
        });
    }
}
```

## Service Decomposition Strategy

### Breaking Down Large Services

Large services like `WorkOrderService` (6068 lines) have been broken down into specialized services:

1. **WorkOrderVendorService** - Handles vendor-related operations
2. **WorkOrderLotService** - Handles lot management
3. **WorkOrderProgressService** - Handles progress tracking
4. **WorkOrderInspectionService** - Handles inspection operations

### Example: Using Specialized Services

```php
class WorkOrderController extends Controller
{
    public function __construct(
        private WorkOrderVendorServiceInterface $vendorService,
        private WorkOrderLotServiceInterface $lotService
    ) {}

    public function getVendors(Request $request)
    {
        return $this->vendorService->getVendorList();
    }

    public function createLot(Request $request)
    {
        return $this->lotService->createLot($request->validated());
    }
}
```

## Migration Strategy

### Step 1: Identify Service Responsibilities

For each large service, identify distinct responsibilities:

- **Data Management**: CRUD operations
- **Business Logic**: Complex calculations and validations
- **Integration**: External service communications
- **Reporting**: Data aggregation and formatting

### Step 2: Extract Specialized Services

Create new services for each responsibility:

```php
// Original large service
class LargeService extends BaseService
{
    // 2000+ lines of mixed responsibilities
}

// Refactored into specialized services
class DataManagementService extends BaseService { }
class BusinessLogicService extends BaseService { }
class IntegrationService extends BaseService { }
class ReportingService extends BaseService { }
```

### Step 3: Update Dependencies

Update controllers and other services to use the new specialized services:

```php
// Before
class Controller
{
    public function __construct(private LargeService $service) {}
}

// After
class Controller
{
    public function __construct(
        private DataManagementServiceInterface $dataService,
        private BusinessLogicServiceInterface $businessService
    ) {}
}
```

## Best Practices

### 1. Transaction Scope

- Keep transactions as short as possible
- Avoid long-running operations within transactions
- Use cross-database transactions only when necessary

### 2. Service Responsibilities

- Each service should have a single responsibility
- Services should not exceed 500-800 lines
- Use composition over inheritance

### 3. Error Handling

```php
try {
    $result = $this->executeWithCrossDbTransaction(function () {
        // Your operations
    });
} catch (\Exception $e) {
    // Log error
    Log::error('Transaction failed', ['message' =>$e->getMessage()]);

    // Return appropriate response
    return ['status' => 'error', 'message' => 'Operation failed'];
}
```

### 4. Testing

```php
class WorkOrderVendorServiceTest extends TestCase
{
    public function test_get_vendor_list()
    {
        $service = app(WorkOrderVendorServiceInterface::class);
        $result = $service->getVendorList();

        $this->assertIsArray($result);
        $this->assertArrayHasKey('vendor_tabs', $result);
    }
}
```

## Performance Considerations

### 1. Connection Pooling

The `ConnectionManager` caches connections to avoid repeated database switches.

### 2. Transaction Optimization

- Use appropriate transaction isolation levels
- Batch operations when possible
- Monitor transaction duration

### 3. Service Instantiation

Use dependency injection to avoid creating multiple instances:

```php
// In service provider
$this->app->singleton(WorkOrderVendorServiceInterface::class, WorkOrderVendorService::class);
```

## Monitoring and Debugging

### 1. Connection Information

```php
$info = ConnectionManager::getConnectionInfo();
// Returns current database connections and cache status
```

### 2. Transaction Status

```php
$hasActive = $transactionManager->hasActiveTransactions();
// Check if there are active transactions
```

### 3. Logging

Add logging to track database operations:

```php
Log::info('Database switch', [
    'from' => $oldDb,
    'to' => $newDb,
    'operation' => 'vendor_list'
]);
```

## Repository Updates

### Updated BaseRepository Classes

Both Global (V2) and Project (V3) BaseRepository classes have been updated to use the new ConnectionManager:

#### Global BaseRepository (V2)

```php
// Updated constructor
public function __construct()
{
    ConnectionManager::resetToGlobalDatabase();
}

// Updated project connection method
public function connectProjectMongoDb($db_name)
{
    ConnectionManager::setProjectDatabase($db_name);
}

// New helper method
public function withProjectDatabase(string $db_name, \Closure $closure): mixed
{
    return ConnectionManager::withProjectDatabase($db_name, $closure);
}
```

#### Project BaseRepository (V3)

```php
// Updated constructor
public function __construct($access)
{
    if ($access && Auth::guard('api')->user()) {
        ConnectionManager::setProjectDatabase($access);
    }
}

// Updated database access method
public function dbAccess($db_name)
{
    ConnectionManager::setProjectDatabase($db_name);
}

// New helper methods
public function withProjectDatabase(string $db_name, \Closure $closure): mixed
{
    return ConnectionManager::withProjectDatabase($db_name, $closure);
}

public function withGlobalDatabase(\Closure $closure): mixed
{
    return ConnectionManager::withGlobalDatabase($closure);
}
```

### Service Provider Registration

Register the new services in your `config/app.php`:

```php
'providers' => [
    // ... other providers
    App\Providers\DatabaseServiceProvider::class,
    App\Providers\WorkOrderServiceProvider::class,
],
```

### Migration Checklist

- [ ] Update BaseRepository classes (completed above)
- [ ] Register DatabaseServiceProvider in config/app.php
- [ ] Test existing repository operations
- [ ] Gradually migrate services to use new transaction methods
- [ ] Update any custom repositories that extend BaseRepository

## Conclusion

This refactoring provides:

1. **Better Transaction Management**: Reliable cross-database transactions
2. **Improved Maintainability**: Smaller, focused services
3. **Enhanced Performance**: Efficient connection management with caching
4. **Better Testing**: Isolated service responsibilities
5. **Cleaner Architecture**: Clear separation of concerns
6. **Consistent Database Handling**: Unified approach across all repositories

### Benefits of Repository Updates:

- **Connection Caching**: Avoids unnecessary database reconnections
- **Consistent API**: Same connection management across Global and Project repositories
- **Better Error Handling**: Centralized connection validation
- **Easier Testing**: Mock ConnectionManager for unit tests
- **Performance Monitoring**: Track database switches and connection usage

Follow this guide to gradually migrate your existing large services to the new architecture.