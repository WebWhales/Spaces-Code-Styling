# DocBlocks

Within a DocBlock, a function can be given extra explanation. IDEs like PhpStorm, LSPs like Intelephense, and static analyzers (like PHPStan) can use docblocks to know the signature or type of a function, parameter or variable.

In general, we should **avoid** docblocks in PHP as much as possible and rely on native types where possible. However, PHP’s type system cannot always express the exact “shape” of data. For these cases, we **do** use DocBlocks with generics or shape.

Use (PHPStan) generics, for example:

- `User[]`
- `array<string, User>`
- `Collection<int, User>`
- `HasMany<Order, $this>`
- `@template TModel of Model`
- etc.

Use PHPStan array or object shaping, for example:

- `object{id: int, name?: string}`
- `array{id: int, name?: string}`

We only add DocBlocks when they **add information that the signature cannot express by itself** (e.g. generics, array element types, collections, templates, complex shapes).

 **Wrong:**

```php
/**
 * This function update the customer account
 *
 * @param User[] $user
 * @param string $name
 * @return bool
 */
public function updateCustomerAccount(array $user, string $name): bool
{
    // Do something.
} 
```

Problems here:

- The description adds almost nothing beyond the function name.
- `@param string $name` and `@return bool` repeat what is already in the signature.
- The only useful extra information is that `$users` is an array of `User`.

> **⚠️ Warning:** Clarifying the function within the DocBlock is only important when it also adds something.

 **Correct:**

Keep only the DocBlock parts that add extra value.

```php
/**
 * @param User[] $user
 */
public function updateCustomerAccount(array $user, string $name): bool
{
    // Do something.
} 
```

More complex shapes:

```php
/**
 * @param array<string user=""> $usersById
 */
function handleUsers(array $usersById): void
{
    // ...
}

/**
 * @return array<int string="">
 */
function process(): array
{
    // ...
}

/**
 * @param array{
 *     id: int,
 *     name: string,
 *     active?: bool
 * } $payload
 */
public function handle(array $payload): void
{
    // ...
}

/**
 * @param object{
 *     id: int,
 *     email: string,
 *     address?: object{
 *         street: string,
 *         city: string
 *     }
 * } $data
 */
public function assign(object $data): void
{
    // ...
}</int></string>
```

## Laravel collections

For `Illuminate\Support\Collection`, Eloquent collections and relations, always document the **value type** (and key type if relevant).

 **Wrong:**

```php
use Illuminate\Support\Collection;

/**
 * @param Collection $users
 * @return Collection
 */
public function getActiveUsers(Collection $users): Collection
{
    // ...
}

```

This doesn’t tell us what the collection contains.

####  **Correct:**

```php
use Illuminate\Support\Collection;
use App\Models\User;

/**
 * @param Collection<int user=""> $users
 * @return Collection<int user="">
 */
public function getActiveUsers(Collection $users): Collection
{
    return $users->filter(fn (User $user) => $user->active);
}
</int></int>
```

Eloquent relations:

```php
use Illuminate\Database\Eloquent\Relations\HasMany;
use App\Models\Order;

/**
 * @return HasMany<order>
 */
public function orders(): HasMany
{
    return $this->hasMany(Order::class);
}
</order>
```

Eloquent collections:

```php
use Illuminate\Database\Eloquent\Collection as EloquentCollection;
use App\Models\Order;

/**
 * @return EloquentCollection<int order="">
 */
public function orders(): EloquentCollection
{
    return $this->hasMany(Order::class)->get();
}
</int>
```

## Templates / generic classes and methods

For reusable components (repositories, services, etc.) that work with **different model types**, use `@template` and `@extends` / `@implements`.

####  **Wrong:**

Non-generic base repository: everything is just `Model`, so you lose type info:

```php
use Illuminate\Database\Eloquent\Model;

abstract class BaseRepository
{
    /** @var class-string<model> */
    protected string $modelClass;

    public function __construct(string $modelClass)
    {
        $this->modelClass = $modelClass;
    }

    public function find(int $id): ?Model
    {
        return $this->modelClass::find($id);
    }

    public function all(): array
    {
        return $this->modelClass::all()->all();
    }
}
</model>
```

Consumers don’t get the concrete model type from the repository.

####  **Correct:**

```php
use Illuminate\Database\Eloquent\Model;

/**
 * @template TModel of Model
 */
abstract class BaseRepository
{
    /** @var class-string<tmodel> */
    protected string $modelClass;

    /**
     * @param class-string<tmodel> $modelClass
     */
    public function __construct(string $modelClass)
    {
        $this->modelClass = $modelClass;
    }

    /**
     * @return TModel|null
     */
    public function find(int $id): ?Model
    {
        return $this->modelClass::find($id);
    }

    /**
     * @return list<tmodel>
     */
    public function all(): array
    {
        return $this->modelClass::all()->all();
    }
}
</tmodel></tmodel></tmodel>
```

Concrete implementation:

```php
use App\Models\User;

/**
 * @extends BaseRepository<user>
 */
final class UserRepository extends BaseRepository
{
    public function __construct()
    {
        parent::__construct(User::class);
    }
}
</user>
```

Generic method example:

```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Collection;

/**
 * @template T of Model
 *
 * @param class-string<t> $modelClass
 * @return Collection<int t="">
 */
public function findActive(string $modelClass): Collection
{
    return $modelClass::query()
        ->where('active', true)
        ->get();
}
</int></t>
```

