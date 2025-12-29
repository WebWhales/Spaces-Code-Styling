# Attributes

## What are attributes (and why use them)?

Attributes are native, structured metadata attached to PHP declarations such as classes, methods, functions, parameters, properties, and constants.

They are a first-class alternative to annotations in docblocks, providing type-safe, structured, and runtime-inspectable metadata.

Attributes can be read via PHP’s `Reflection` API and are also recognized by static analysis tools, IDEs, and language servers.

> [RFC Proposal](https://wiki.php.net/rfc/attributes_v2)
> 
> Introduced in **PHP 8.0**, attributes allow developers to declare behavior declaratively instead of imperatively. Without relying on comments or external configuration.

### Why attributes?

The advantage of using attributes is to make metadata in code:

- Type-safe
- Easier to reflect on
- Less error-prone
- Better supported by tools (LSP, IDEs, static analyzers)

Besides that, it offers a new way for frameworks to implement features in a more organized, logical and clean way.

**Before attributes:**

Before PHP 8.0, metadata was typically stored in docblocks:

```php
/**
 * @deprecated Use NewClass instead.
 */
class OldClass {}

class User extends Model
{
    public function boot(): void
    {
        User::observe(UserObserver::class);
    }
}
```

**With attributes:**

With attributes, metadata becomes part of the language syntax:

```php
#[Deprecated('Use NewClass instead.')]
class OldClass {}

#[ObservedBy([UserObserver::class])]
class User extends Model
{
    //
}
```

## When and when not to use attributes

Attributes are best suited for declarative metadata. When you want to describe **what** behavior a code element should have, rather than **how** to perform it.

Think of attributes as configuration, not logic. They should define intent, not execute behavior.

```php
#[Validate(['required', 'email'])]
public string $email;

#[Route('/users', methods: ['POST'])]
public function createUser() {}

#[MapFrom('user_id')]
public int $id;
```

```php
#[LogSomething]
class User {}
```

> **⚠️ Error:** Attributes should never perform runtime operations, I/O, or heavy computations. They should only hold data. The system using them decides when and how to act.

```php
#[Cache(ttl: time() + 3600)]
function fetchUsers() {}
```

> **⚠️ Error:** Attribute arguments should be static, deterministic values (scalars, arrays, constants, or class names). You should not perform calculations or call functions inside them.

**Use attributes when:**

- You need structured metadata (e.g., routing, casting, validation).
- You want to replace repetitive boilerplate configuration.
- The metadata is essential to runtime behavior.

**Avoid attributes when:**

- The logic requires heavy computation or side effects inside the attribute.
- Metadata is purely decorative or non-essential.
- Using them would reduce readability due to clutter.

Keep attribute classes simple and lightweight. They should describe behavior, not execute it.

In a package, you can take the most advantage of attributes to allow users to declaratively configure behavior without needing to write boilerplate code. This leads to cleaner, more maintainable code.

## Common attributes

### Laravel attributes

For the container/contextual attribute, you can see this example from the Laravel doc: [https://laravel.com/docs/12.x/container#contextual-attributes](https://laravel.com/docs/12.x/container#contextual-attributes)

| **Attribute** | **Used from/with** | **Purpose / Usage** |
|---|---|---|
| **CollectedBy** | Eloquent models | Specify a custom Eloquent Collection class for the model. |
| **ObservedBy** | Eloquent models | Declare observers for the model. |
| **Scope** | Eloquent models | Declare a static method to be a query scope. |
| **ScopedBy** | Eloquent models | Declare global query scopes for a model. |
| **DeleteWhenMissingModels** | Queued jobs | Auto-delete job if its model(s) no longer exist. |
| **WithoutRelations** | Queued jobs | Exclude relations when serializing models in a job. |
| **Auth** | Laravel Container | Inject an auth **Guard** by name (e.g. `"web"`). |
| **CurrentUser** | Laravel Container | Inject the current authenticated **User**. |
| **Cache** | Laravel Container | Inject a cache repository by store name. |
| **Config** | Laravel Container | Inject a config value by key. |
| **Context** | Laravel Container | Inject contextual app data value by key. |
| **DB** | Laravel Container | Inject a DB connection. |
| **Log** | Laravel Container | Inject a logger instance (by channel name). |
| **RouteParameter** | Laravel Container | Inject a route parameter (or model bound). |
| **Storage** | Laravel Container | Inject a storage disk. |
| **Tag** | Laravel Container | Inject all services tagged with a given tag name. |
| **Give** | Laravel Container | Provide a specific class implementation for an interface (for this injection). |
| **Bind** | Laravel Container | On an *interface:* bind it to an implementation (with optional variants). |
| **Singleton** | Laravel Container | Mark binding as singleton (one global instance). |
| **Scoped** | Laravel Container | Mark binding as scoped (one instance per request/job). |

### PHPUnit attributes

| **Attribute** | **Framework** | **Purpose / Usage** |
|---|---|---|
| **BeforeClass** | Lifecycle | Static method to run **once before all tests** in class (supports priority). |
| **AfterClass** | Lifecycle | Static method to run **once after all tests** in class. |
| **Before** | Lifecycle | Method to run **before each test** (like setUp). |
| **After** | Lifecycle | Method to run **after each test** (like tearDown). |
| **PreCondition** | Lifecycle | Method to run before each test’s body (after Before methods). |
| **PostCondition** | Lifecycle | Method to run after each test’s body (before After methods). |
| **DataProvider** | Dataset | Use a static **method** (same class) as data provider for test. |
| **DataProviderExternal** | Dataset | Use a static method in **another class** as data provider. |
| **TestWith** | Dataset | Provide **inline data set(s)** for a test (no method needed). |
| **TestWithJson** | Dataset | Provide inline data via a JSON string (for complex data). |
| **WithoutErrorHandler** | Behaviour | Disable PHPUnit’s error-to-exception handler for this test (allow PHP native error behavior). |
| **Depends** | Dependency | Test **depends on another test** in same class (use its result). |
| **DependsUsingDeepClone** | Dependency | Depends on another test (clone its result deeply). |
| **DependsUsingShallowClone** | Dependency | Depends on another test (clone result shallowly). |
| **DependsExternal** | Dependency | Depends on a test in **another class** (no cloning). |
| **DependsExternalUsingDeepClone** | Dependency | External dependency (deep clone result). |
| **DependsExternalUsingShallowClone** | Dependency | External dependency (shallow clone result). |
| **DependsOnClass** | Dependency | Depends on *all tests* of another test class. |
| **DependsOnClassUsingDeepClone** | Dependency | Depends on all tests of class (deep clone values). |
| **DependsOnClassUsingShallowClone** | Dependency | Depends on all tests of class (shallow clone). |
| **RequiresPhp** | Test conditions | Skip test unless running on specified **PHP version** (supports `>=`, ranges). |
| **RequiresPhpExtension** | Test conditions | Skip unless a **PHP extension** (and optional version) is available. |
| **RequiresSetting** | Test conditions | Skip unless a PHP **ini setting** has a given value. |
| **RequiresPhpunit** | Test conditions | Skip unless running on a specific **PHPUnit version**. |
| **RequiresPhpunitExtension** | Test conditions | Skip unless a **PHPUnit extension** (by class) is loaded. |
| **RequiresFunction** | Test conditions | Skip if a **function** is not defined (e.g. missing extension function). |
| **RequiresMethod** | Test conditions | Skip if a **method** (in a class) is not present. |
| **RequiresOperatingSystem** | Test conditions | Skip unless OS matches a given **regex** (PHP\_OS). |
| **RequiresOperatingSystemFamily** | Test conditions | Skip unless OS family matches (Windows, Linux, etc.). |
| **RequiresEnvironmentVariable** | Test conditions | Skip unless an **environment variable** is set (and optional value). |
| **Group** | Grouping | Assign test to a **group** (category). Repeatable for multiple groups. |
| **Ticket** | Grouping | Alias for Group (e.g., link to issue/ticket ID). |
| **TestDox** | Output | Define a custom **description** for TestDox output (supports placeholders). |
| **TestDoxFormatter** | Output | Use a **formatter method** (in same class) to generate TestDox text. |
| **TestDoxFormatterExternal** | Output | Use a formatter method in **another class** for TestDox. |
| **Small** | Test size | Mark tests in class as **small** (fast, unit). Affects timeouts & coverage. |
| **Medium** | Test size | Mark tests in class as **medium**. |
| **Large** | Test size | Mark tests in class as **large** (slow, integration). |
| **BackupGlobals** | Isolation | **Backup all globals** and superglobals for test (restore after). |
| **ExcludeGlobalVariableFromBackup** | Isolation | Don’t backup a specific global (e.g. `$_SERVER`). |
| **BackupStaticProperties** | Isolation | **Backup static properties** of classes for test. |
| **ExcludeStaticPropertyFromBackup** | Isolation | Exclude a specific static property from backup. |
| **RunInSeparateProcess** | Isolation | Run this test method in a **separate PHP process**. |
| **RunTestsInSeparateProcesses** | Isolation | Run *each test* in class in its own process. |
| **RunClassInSeparateProcess** | Isolation | Run all tests in class in one separate process. |
| **PreserveGlobalState** | Isolation | Control whether to **preserve global state** when using separate processes. (e.g., `PreserveGlobalState(false)` to isolate completely). |
| **CoversClass** | Coverage | Declare a class that the test *intends to cover*. |
| **CoversMethod** | Coverage | Declare a specific class **method** covered by test. |
| **CoversFunction** | Coverage | Declare a standalone **function** covered by test. |
| **CoversTrait** | Coverage | Declare a **trait** covered by test. |
| **CoversClassesThatImplementInterface** | Coverage | Test covers all implementations of an **interface**. |
| **CoversClassesThatExtendClass** | Coverage | Test covers all subclasses of a given **class**. |
| **CoversNothing** | Coverage | Test is not intended to cover any code (exclude from coverage). |
| **UsesClass** | Coverage | Marks a class as **used** by the test (not counted as covered). |
| **UsesTrait** | Coverage | Marks a trait as used by the test (not counted as covered). |
| **UsesFunction** | Coverage | Marks a function as used by the test (not counted as covered). |
| **UsesClassesThatImplementInterface** | Coverage | Allows execution of classes implementing interface (not covered). |
| **UsesClassesThatExtendClass** | Coverage | Allows execution of subclasses of a class (not covered). |
| **UsesMethod** | Coverage | Allows execution of a specific method (not covered). |

## Using/implementing custom attributes

The following example demonstrates how attributes can simplify DTO construction, type casting, and data mapping.

### Define attribute classes

Here we are just telling PHP that his classes are attributes that can be assigned to a parameter of a class. This also helps your IDE/LSP to validate the correct use of the attribute.

```php
#[Attribute(Attribute::TARGET_PARAMETER)]
final class MapFrom
{
    public function __construct(public string $path) {}
}

#[Attribute(Attribute::TARGET_PARAMETER)]
final class CastWith
{
    public function __construct(public string $caster) {}
}

#[Attribute(Attribute::TARGET_PARAMETER)]
final class DateTimeFormat
{
    public function __construct(public string $format) {}
}
```

In the section of the hydrator, we can see how we can get that metadata and initialize these attribute classes.

### Contract

Define a simple contract to ensure consistency for custom casters:

```php
interface CastsValue
{
    public function cast(mixed $value, array $context = []): mixed;
}
```

### Casters

```php
final class IntCaster implements CastsValue
{
    public function cast(mixed $value, array $context = []): ?int
    {
        if ($value === null || $value === '') return null;
        return is_numeric($value) ? (int)$value : null;
    }
}

final class BoolCaster implements CastsValue
{
    public function cast(mixed $value, array $context = []): bool
    {
        return filter_var($value, FILTER_VALIDATE_BOOL, FILTER_NULL_ON_FAILURE) ?? false;
    }
}

final class DateTimeCaster implements CastsValue
{
    public function cast(mixed $value, array $context = []): ?DateTimeImmutable
    {
        if ($value === null || $value === '') return null;
        $format = $context['format'] ?? 'c';
        $dt = DateTimeImmutable::createFromFormat($format, (string)$value);
        return $dt ?: null;
    }
}

// etc, you can make your own caster
```

### Hydrator

```php
final class DtoHydrator
{
    private static array $cache = [];

    public static function hydrate(string $class, array $input): object
    {
        $meta = self::$cache[$class] ??= self::inspect($class);
        $args = [];

        foreach ($meta['params'] as $m) {
            $raw = data_get($input, $m['map']);
            $val = $raw;

            if ($m['caster']) {
                /** @var CastsValue $caster */
                $caster = new ($m['caster']);
                $ctx = $m['format'] ? ['format' => $m['format']] : [];
                $val = $caster->cast($raw, $ctx);
            }

            if ($val === null && !$m['hasDefault'] && !$m['param']->allowsNull()) {
                throw new InvalidArgumentException("Missing field: {$m['map']}");
            }

            $args[] = $val ?? $m['default'];
        }

        return (new ReflectionClass($class))->newInstanceArgs($args);
    }

    private static function inspect(string $class): array
    {
        $r = new ReflectionClass($class);
        $ctor = $r->getConstructor();
        if (!$ctor) return ['params' => []];

        $params = array_map(function (ReflectionParameter $p) {
            $map    = self::attr($p, MapFrom::class)?->path ?? $p->getName();
            $caster = self::attr($p, CastWith::class)?->caster ?? null;
            $format = self::attr($p, DateTimeFormat::class)?->format ?? null;

            return [
                'map'        => $map,
                'caster'     => $caster,
                'format'     => $format,
                'hasDefault' => $p->isDefaultValueAvailable(),
                'default'    => $p->isDefaultValueAvailable() ? $p->getDefaultValue() : null,
                'param'      => $p,
            ];
        }, $ctor->getParameters());

        return ['params' => $params];
    }

    private static function attr(ReflectionParameter $p, string $attr): ?object
    {
        $a = $p->getAttributes($attr);
        return $a[0]?->newInstance() ?? null;
    }
}

```

### Using example: CreateUserDTO

```php
final readonly class CreateUserDTO
{
    public function __construct(
        #[MapFrom('first_name')]
        public string $firstName,

        #[MapFrom('last_name')]
        public string $lastName,

        #[MapFrom('email')]
        public string $email,

        #[MapFrom('age'), CastWith(IntCaster::class)]
        public ?int $age = null,

        #[MapFrom('is_marketing_opt_in'), CastWith(BoolCaster::class)]
        public bool $marketingOptIn = false,

        #[MapFrom('birthdate'), CastWith(DateTimeCaster::class), DateTimeFormat('Y-m-d')]
        public ?DateTimeImmutable $birthdate = null,
    ) {}

    public static function fromArray(array $validated): self
    {
        return DtoHydrator::hydrate(self::class, $validated);
    }
}

```

