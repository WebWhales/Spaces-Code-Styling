# Interfaces

## Naming & Namespace Conventions

- Use the `Interface` suffix for interface names only when it improves clarity
- Avoid prefixing interfaces with `I` (e.g., `IUser`).
- Place interfaces in a dedicated `App\Contracts` namespace in the folder `app/Contracts`.
- When the project has many interface, group related interfaces in sub namespaces, e.g.:
    - `app/Contracts/Payment/GatewayInterface.php`.
    - `app/Contracts/User/AuthInterface.php`.
    - `app/Contracts/Models/UserInterface.php`.

## When to Use Interfaces

### Define structure

Use interfaces to define **multiple implementations** of an action or behavior.

```php
interface PaymentGatewayInterface {
    public function charge(string $customerId, int $amount, string $currency): string;
}

class StripePaymentGateway implements PaymentGatewayInterface {
    public function charge(string $customerId, int $amount, string $currency): string
    {
        // Call Stripe
    }
}

class MolliePaymentGateway implements PaymentGatewayInterface {
    public function charge(string $customerId, int $amount, string $currency): string
    {
        // Call Mollie
    }
}
```

### Testability / Fakes / Mocks

Use interfaces to allow for **easy mocking or faking in tests**.

```php
interface Clock {
    public function now(): \DateTimeImmutable;
}

class SystemClock implements Clock {
    public function now(): \DateTimeImmutable
    {
        return new \DateTimeImmutable();
    }
}

class FrozenClock implements Clock {
    public function __construct(private \DateTimeImmutable $t) {}

    public function now(): \DateTimeImmutable
    {
        return $this->t;
    }
}
```

### Insulate volatile dependencies

Use interfaces to **insulate functionality** that is **likely to change or be replaced**.

```php
interface FeatureFlagStore {
    public function enabled(string $key, int|string|null $for = null): bool;
}
// Implementations: LaunchDarkly, DB, Redis.
```

## When Not to Use Interfaces

**Only one implementation exists (and that’s unlikely to change).**

An interface adds complexity; you can always extract one later if needed.

> **ℹ️ Note:** A commonly cited policy states that all services must have an interface, but this is not good practice because it adds unnecessary complexity to the codebase.

```php
// Unnecessary interface + class pair for a simple service
interface Slugger {
    public function slug(string $title): string;
}

class SimpleSlugger implements Slugger {
    // ...
}
```

**Pure value objects / DTOs.**

Interfaces don’t add value to immutable data carriers.

**Hiding framework specifics without a need.**

Don’t introduce `ControllerInterface`, `FormRequestInterface`, etc. Laravel already defines stable contracts; create your own only if you truly require a seam.

## Tips

- Extract an interface that expresses only the essential behavior.
- Make the existing class implement the interface and update type hints.
- Keep method names and return types strict; avoid mixed.

