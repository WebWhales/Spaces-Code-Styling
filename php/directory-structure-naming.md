# Directory Structure & Naming

## What DTOs are:

In our projects a DTO (“Data Transfer Object” / “Data Object”) is:

- A small PHP class describing **the shape of data** (fields, types).
- Often extends `Spatie\LaravelData\Data` from [spatie/laravel-data](https://spatie.be/docs/laravel-data/).
- Has **little to no business logic** (no DB queries, no side effects).

We use DTOs mainly to:

- Make data **explicitly typed** instead of passing raw arrays.
- Separate **HTTP / API layer** from the **domain / persistence layer**.
- Wrap external API payloads so they don’t leak all over the codebase.

All data classes are stored in `app/Data/`.

We can organize the files in two ways depending on the size or set-up of the project.

For small projects:

- `app/Data/`

For large projects, when the `Data` folder contains many files, or when we the project contains a Domain/Modules structure:

- `app/Data/User/`
- `app/Data/Order/`
- `app/Data/External/Stripe/`

### Naming Convention

Data classes describe the entity they are representing. Class names should not have a `Data` suffix, unless it is a part of the entity name.

**Examples:**

- `Person`
- `OrderItem`
- `StripeWebhookPayload`
- `ProjectMetaData`

## When do we use DTOs?

### Crossing module/service boundaries

Use DTOs to pass data between **modules/services** instead of loose arrays, especially when:

- The structure is reused in multiple places.
- The data represents a concept in the domain (e.g. `Price`, `Address`).

### External API integration

Use DTOs for **request/response** to external services:

- Map **raw JSON → DTO** at the boundary
- Application code works only with DTOs
- Convert back to array/JSON only in the adapter layer

**Example:**

```php
class ApiUser extends Data
{
    public function __construct(
        public string $email,
        public string $externalId,
        public ?string $cardNumber,
    ) {}

    public static function fromApi(array $payload): self
    {
        return new self(
            email: $payload['email'],
            externalId: $payload['id'],
            cardNumber: $payload['card_number'] ?? null,
        );
    }

    public function toApiPayload(): array
    {
        return [
            'email' => $this->email,
            'id' => $this->externalId,
            'card_number' => $this->cardNumber,
        ];
    }
}
```

### JSON columns on models

We sometimes store structured data in JSON columns (e.g. `settings`, `metadata`, `profile`, …). There are several options for handling these:

For **small, stable structures** that are accessed occasionally (e.g. user settings with a few keys), it’s OK to use a DTO directly as a cast

```php
protected $casts = [
    'settings' => \App\Data\UserSettings::class,
];
```

Use when:

- JSON is small
- Access frequency is low
- Schema is stable

Laravel will:

- Decode + build DTO **once** per model instance
- Cache the DTO object in `classCastCache`
- Reuse the object on subsequent reads of **the same model instance**

#### Performance Note: Why DTO Casts Can Still Be Slow

Although Laravel caches the **object** returned by a class-based cast:

- When you **assign** the value (write), the cast’s `set()` method is called
- `set()` must stringify JSON for storage
- Laravel invalidates the cached cast value
- The very next read requires another decode + DTO creation

This means loops like:

```php
foreach ($users as $user) {
    $settings = $user->settings;     // decode + DTO
    $settings->foo = true;
    $user->settings = $settings;     // encode + invalidates cache
}
```

For larger or frequently accessed JSON columns

```php
protected $casts = [
    'metadata' => 'array',
];

// Map to a DTO only where needed (controller/service)
$metadata = UserMetaData::from($user->metadata);
```

## When not to use DTOs as casts

Avoid DTO casts when:

- The JSON is **large or deeply nested**
- The attribute is accessed and written many times per request (cache invalidated on each write)
- You load many models at once (lists, exports) — cost is multiplied

## Understanding Laravel Cast Caching

For class-based casts (like Spatie Data or custom cast):

- Laravel caches the **object** returned by `get()`
- Reuses that object for the same model instance
- Cache invalidated when:
    
    
    - You assign to the attribute (`set()`)
    - You reload attributes
    - You call `refresh()`

### Important Clarification

Writing inside the object does NOT trigger serialization:

```php
$user->settings->foo = true; // Does NOT call set(), does NOT save
$user->save();               // JSON unchanged

```

You must always reassign:

```php
$settings = $user->settings;
$settings->foo = true;
$user->settings = $settings; // set() now runs
$user->save();
```

This behavior applies to all custom casts, including Spatie Data.

## Another approach with AsArrayObject

Laravel offers an alternative way to work with JSON columns using `AsArrayObject`, which behaves very differently from DTO-based casts. This approach is useful when you want **flexible, mutable JSON** without the overhead or strictness of DTOs.

### Option 1: Custom/Spatie Data Casts

Best for:

- Strong typing
- Domain-driven data structures
- API request/response mapping
- Validation at boundaries

Pros:

- Strong schema
- Great IDE support
- Clear domain meaning

Cons:

- Mutating values requires **explicit reassignment**
- Heavy read→write loops are slow
    
    
    - Writes invalidate classCastCache
    - Next read triggers full decode + DTO build

- Not ideal for large JSON columns

Use when:

- You need structured, meaningful, strongly typed objects.

### Option 2: AsArrayObject

`AsArrayObject` is designed specifically for **mutable JSON blobs** such as user settings, metadata, or preferences.

This cast allows:

```php
$user->settings['theme'] = 'dark';
$user->save();
```

and Laravel **will detect the mutation and persist it**, without requiring reassignment.

Pros:

- Supports in-place mutation
- Laravel tracks changes and serializes automatically
- No explicit reassignment needed
- Good for flexible or frequently updated JSON

Cons:

- No type safety
- Weaker IDE support
- No schema enforcement
- Not suitable for domain-critical structured data

Use when:

- You want ergonomic JSON mutation
- You don’t need strict typing
- Data structure is flexible or user-generated (settings, metadata)

## Using spatie/laravel-data

To create a DTO with `laravel-data`, extend `Data`:

```php
enum PostStatus: string
{
    case draft = 'draft';
    case published = 'published';
    case archived = 'archived';
}

use Spatie\LaravelData\Data;

class PostData extends Data
{
    public function __construct(
        public string $title,
        public string $content,
        public PostStatus $status,
        public ?CarbonImmutable $published_at
    ) {
    }
}
```

**Instantiating**

```php
$post = new PostData(
    'Hello laravel-data',
    'This is an introduction post for the new package',
    PostStatus::published,
    CarbonImmutable::now()
);
```

**Using** `<strong>from()</strong>`

```php
$post = PostData::from([
    'title' => 'Hello laravel-data',
    'content' => 'This is an introduction post for the new package',
    'status' => PostStatus::published,
    'published_at' => CarbonImmutable::now(),
]);
```

**Using as an Eloquent cast type**

```php
class Log extends Model
{
    protected $casts = [
        'post' => PostData::class,
    ];
}
```

