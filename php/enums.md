# Enums

Enums must be used when you need a predefined list of values. For this purpose enums are also a replacement for (class) constants.

## Naming conventions

Enum names follow the same guidelines as [class names](/spaces/programmeren/page/5149-classes#naming-conventions).

Enum classes must be written in PascalCase.

 **Example**

```php
enum OrderStatus
{
    case New;
    case Pending;
    case Processing;
    case Completed;
}
```

## Backed enums

Enum cases may be *backed* with textual (or numeric) representing values in order to save them in the database, use them in routes etc. Without backed cases, enums can only be used within the code.

When enums are only used in code and thus never stored as text, enums should not be backed.

Cases should be backed by textual values, unless the enum is representing numbers.

 **Example**

```php
enum OrderStatus: string
{
    case New = 'new';
    case Pending = 'pending';
    case Processing = 'processing';
    case Completed = 'completed';
}
```

 **Example**

```php
enum OrderPaidStatus: int
{
    case Unpaid = 0;
    case Paid = 1;
}
```

## Using Methods in Enums

Only simple match methods are allowed

 **Example**

```php
<?php namespace App\Enums;

enum OrderStatus: string
{
    case Pending = 'pending';
    case Processing= 'processing';
    case Completed = 'completed';
    case Cancelled = 'cancelled';

    public function label(): string
    {
        return match ($this) {
            OrderStatus::Pending    =?> 'Pending',
            OrderStatus::Processing => 'Processing',
            OrderStatus::Completed  => 'Completed',
            OrderStatus::Cancelled  => 'Cancelled',
        };
    }

    public function isFinal(): bool
    {
        return $this === self::Completed || $this === self::Cancelled;
    }

    public static function default(): self
    {
        return self::Pending;
    }
}

// Usage

$status = OrderStatus::Processing;

echo $status->label(); // “Processing”

if ($status->isFinal()) {
    // do something
}

$post->status = OrderStatus::Completed;
```

## Namespace convention

### Laravel specific

- Inside the app folder, a folder named Enums should be created, which should contain Enums directly, or further sub folders can be created to group them by domain.
    
    
    - Example: `app/Enums/UserStatus.php`
    - Or: `app/Enums/User/UserStatus.php`
- The namespace should directly correlate to the folder structure, just like with [PHP classes](https://webwhales.teamwork.com/spaces/programmeren/page/5149-classes).
- Name your enum classes in a way that makes clear **what domain or column they represent** (e.g. `PostStatus`, `UserRole`, `OrderState`).

### WordPress specific

- We use the existing folder structure of the plugin, theme or child theme that is in use.
    - For the Child theme of the Base theme, we can place them here: `wp-content/themes/{project_name}-wp-child-theme/inc/enums`
- The namespace should directly correlate to the folder structure, just like with [PHP classes](https://webwhales.teamwork.com/spaces/programmeren/page/5149-classes). Unless the plugin, theme or child theme has established otherwise.
- Name your enum classes in a way that makes clear **what domain or column they represent** (e.g. `PostStatus`, `UserRole`, `OrderState`).

## Sources

- [https://wiki.php.net/rfc/enumerations](https://wiki.php.net/rfc/enumerations)
- [https://spatie.be/guidelines/laravel-php#content-enums](https://spatie.be/guidelines/laravel-php#content-enums)

