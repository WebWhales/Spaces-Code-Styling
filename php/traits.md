# Traits

## Naming conventions

Trait names follow the same guidelines as [class names](/spaces/programmeren/page/5149-classes#naming-conventions).

Traits must be written in PascalCase and must be written as an adjective or a characteristic.

  **Example definition**

```php
namespace App\Traits;

trait Loggable
{
    public function logInfo(string $message): void
    {
        // assume $this->logger is present
        $this->logger->info($message);
    }
}
```

```php
namespace App\Traits;

trait SendsNotifications
{
    public function notify(Notifiable $recipient, string $message): void
    {
        // ...
    }
}
```

 **Example usage**

```php
namespace App\Models;

use App\Traits\Loggable;

class User extends Model
{
    use Loggable;
    use SendsNotifications;
}
```

## Namespace convention

### Laravel specific

- Inside the app folder, a folder named Traits should be created, which should contain Traits directly, or further sub folders can be created to group them by domain.
    
    
    - Example: `app/Traits/Loggable.php`
    - Or: `app/Traits/User/HasUuids.php`
- The namespace should directly correlate to the folder structure, just like with [PHP classes](https://webwhales.teamwork.com/spaces/programmeren/page/5149-classes).
- Name your traits in a way that makes clear what their functionality entails.

### WordPress specific

- We use the existing folder structure of the plugin, theme or child theme that is in use.
    - For the Child theme of the Base theme, we can place them here: `wp-content/themes/{project_name}-wp-child-theme/inc/traits`
- The namespace should directly correlate to the folder structure, just like with [PHP classes](https://webwhales.teamwork.com/spaces/programmeren/page/5149-classes). Unless the plugin, theme or child theme has established otherwise.
- Name your traits in a way that makes clear what their functionality entails.

## When to Use

### Use traits when:

- You write behavior needed by many classes that do not share a common parent
    - Useful for small utility / helper classes, e.g. `SoftDeletes`, `HasUuids`, `HasTimestamps`, `Loggable`
- Avoiding duplication, using traits makes it so that you don't have to write the same methods repeatedly in multiple classes that have no inheritance to each other.
- When trait initialization is required **-** Laravel supports “booting” trait methods (e.g. `bootTraitName`) to run logic when an Eloquent model boots.
- Traits allow you to design your code in modular chunks, improving maintainability.

###  Don't use traits when:

- The logic is self-contained and reusable outside of classes. If the code doesn’t depend on `$this` or class context, a plain helper function or action class is clearer and easier to maintain.
    - For example `Str::slug($title)` or `DateHelper::format()` don’t belong in a trait.
- Traits provide static behavior. If you need variation (different behavior depending on the context), use interfaces instead.

## Best practices

- A trait should *not* assume that the using class has certain properties or methods unless explicitly documented. Those dependencies become implicit and brittle.
- Avoid traits that bundle many responsibilities, they become hard to maintain. A trait should ideally have one clearly defined purpose.
- Traits should be small and concise, they should have a clearly defined purpose and responsibility.

## Sources

- [https://www.php.net/manual/en/language.oop5.traits.php](https://www.php.net/manual/en/language.oop5.traits.php)
- [https://medium.com/%40amirnagy886/booting-a-trait-convention-in-laravel-08349e90bd8b](https://medium.com/%40amirnagy886/booting-a-trait-convention-in-laravel-08349e90bd8b)
- [https://spatie.be/guidelines/laravel-php#content-traits](https://spatie.be/guidelines/laravel-php#content-traits)
- [https://www.php-fig.org/bylaws/psr-naming-conventions/](https://www.php-fig.org/bylaws/psr-naming-conventions/)
- [https://www.linkedin.com/pulse/mastering-php-traits-why-when-how-use-them-real-world-mati-ullah-sqgoe/](https://www.linkedin.com/pulse/mastering-php-traits-why-when-how-use-them-real-world-mati-ullah-sqgoe/)

