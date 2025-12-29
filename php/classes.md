# Classes

## Naming conventions

> **ℹ️ Note:** For this section, I will refer to the naming conventions used within Laravel. Note that I will be mentioning the name of the file, keeping in mind that the files comply with PSR-4 (where the name of the file is the same as the class). 

**Laravel specific**

In Laravel, we have various files very frequent in an application with different purposes, to have an idea of the organization of these files we can see in the [official Laravel documentation](https://laravel.com/docs/master/structure).

The class name should always be written with PascalCase/StudlyCaps as suggested by [PSR-1](https://www.php-fig.org/psr/psr-1/#3-namespace-and-class-names). Most of these files/classes are suffixed with their functionality (e.g. NameController, NameJob, NameRequest, etc.) with some exceptions (e.g. Models, Enums, Helpers).

**WordPress specific**

In WordPress we use class names that describe the use/functionality of the class. For example `ChildTheme/General/AcfFields.php` should contain logic that is relevant to ACF fields, such as adding fields/groups. The class name should always be written with PascalCase/StudlyCaps as suggested by [PSR-1](https://www.php-fig.org/psr/psr-1/#3-namespace-and-class-names).

Note: For WordPress we use the existing autoloading structure of the respective theme, we don't have to alter it in order to comply with \[[PSR-0](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md)/[PSR-4](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader.md)\].

## Visibility

### Private by default

All constants, methods and properties need to be declared private by default and can be declared more public whenever necessary.

This helps to maintain the code, since a property that is private tells us that this property only exists within the class only. When I want to do a refactoring, I don't have to worry that this property was used in another file.

 Correct

```php
public string $variableUsedOutsideClass;

private string $variableOnlyUsedWithinClass;
```

### Ordered by visibility

Constants, properties and methods are ordered by visibility (public - protected - private). 
This increases readability for anyone who has to use your class from the outside.

 Correct

```php
public string $a;

protected string $b;

private string $c;
```

## Constructor property promotion

Whenever possible, use constructor promoted properties instead of property initialization.

 Correct

```php
public function __construct(private string $a, private int $b = 0)
{
    // No initialization required for $a and $b
}
```

 Wrong

```php
private string $a;

private int $b;

public function __construct(string $a, int $b = 0)
{
    $this->a = $a;
    $this->b = $b;
}
```

## Ordering constants, properties and methods

Within classes, we order constants, properties and methods as follows:

1. Constants
2. Properties
    Contextual before static
3. Constructor en destructor (regardless of visibility)
4. Other magic methods
    Contextual before static, logically ordered within visibility ordering
5. Methods
    Contextual before static, logically ordered within visibility ordering
    1. Getters and setters (e.g. `function getStatus()` and `function setStatus()`) should always be kept together.

Example of ordered class:

 Correct

```php
class A
{
    /*
     * Constants
     */
    const STATUS_ACTIVE = 'active'; // public by default

    protected const FOO = 'foo';

    private const BAR = 'bar';

    /*
     * Properties
     */
    public ?string $a;

    protected int $b = 0;

    private bool $c = false;

    /*
     * Static properties
     */
    public static array $d = [];
    
    protected static ?object $e;
    
    private static float $f = 0.0;

    /*
     * Constructor (with promoted properties) and destructor
     */
    public function __construct(
        public    string $status,
        protected int    $foo,
        private   bool   $bar = false,
        private   object $o = new stdClass // >= PHP 8.1
    ) {
        // Do something
    }

    public function __destruct()
    {
        // Do something when object is destroyed
    }

    /*
     * Other magic methods
     */
    public function __get(string $name)
    {
        // Magic getter
    }

    public function __set(string $name, mixed $value)
    {
        // Magic setter
    }

    /*
     * Contextual methods
     */
    public function doSomething(): void
    {
        // Do something
    }

    protected function doSomethingProtected(): void
    {
        // Do something
    }

    private function doSomethingPrivate(): void
    {
        // Do something
    }

    
    /*
     * Static methods
     */
     public static function doSomethingStatic() 
     {
         // Do something static
     }
}
```

## Chaining in class methods

### **When to use chaining methods:**

1. **For fluent configuration**
    
    
    - Building an object step by step (e.g., `setName()->setEmail()->setRole()`)
    - Makes the code shorter and more readable.
    - For example in query builders, form builders, or other “fluent interfaces”
2. **To avoid temporary variables**
    
    
    - No need to write `$obj->setX(); $obj->setY();` repeatedly; the chain handles it in one statement.

---

### **When not to use chaining methods:**

1. **Methods with complex side effects**
    
    
    - If a method modifies external resources or can throw errors
2. **Immutable objects**
    
    
    - If each method should return a new object instead of mutating the current one, `return $this` doesn’t work well.

---

### **What to avoid when using chaining methods:**

1. **If readability suffers** 
    - Very long chains or unclear methods make the code harder to follow.
        
         Example of long chains and unclear methods:
        ```php
        $order = (new Order())
            ->setCustomer("Alice")
            ->setShippingAddress("123 Main Street")
            ->setBillingAddress("123 Main Street")
            ->addItem("Laptop", 1, 1200.00)
            ->addItem("Mouse", 2, 25.00)
            ->applyDiscount("SUMMER2025")
            ->calculateTaxes()
            ->finalize()
            ->sendConfirmationEmail()
            ->archive()
            ->logToCRM();
        
        ```
2. **Non-intuitive combinations**
    
    
    - If the methods in the chain don’t logically follow each other, chaining can be confusing.
        
         Example of a non-intuitive combination:
        
        ```php
        class ShoppingCart {
            private array $items = [];
        
            public function addItem(string $item): self {
                $this->items[] = $item;
                return $this;
            }
        
            public function checkout(): self {
                // logic
                return $this;
            }
        
            public function addDiscountCode(string $code): self {
                // logic
                return $this;
            }
        }
        
        // Technically valid chain:
        $cart = (new ShoppingCart())
            ->addItem("Laptop")
            ->checkout()
            ->addDiscountCode("SUMMER2025"); // ❌ logically unintuitive since it takes place after checkout
        ```

 Correct example of class method chaining:

```php
class User {
    private string $name;
    private string $email;

    public function setName(string $name): self {
        $this->name = $name;
        return $this;
    }

    public function setEmail(string $email): self {
        $this->email = $email;
        return $this;
    }

    public function getInfo(): string {
        return "Name: {$this->name}, Email: {$this->email}";
    }
}

// Usage
$user = (new User())
    ->setName('John')
    ->setEmail('johndoe@example.com');

echo $user->getInfo();

```

## Read-only properties and classes

### **When to use** `<strong>readonly</strong>`

1. In situations where important data needs to be protected from (accidental) changes
2. For objects that should never change after creation.
3. When clear intent needs to be shown. Using readonly makes it obvious to other developers which data should not be modified.

**Single immutable properties** – Use property-level `readonly` when only some properties need protection.

**Class-level immutability** – Use class-level `readonly` when the entire object should be immutable.

---

 Example of read-only properties:

```php
class User {
    public readonly string $username;
    public readonly string $email;

    public function __construct(string $username, string $email) {
        $this->username = $username;
        $this->email = $email;
    }
}

$user = new User('Alice', 'alice@example.com');
echo $user->username; // works
$user->username = 'Bob'; // ❌ Error: readonly property can't be changed
```

 Example of read-only classes:

```php
readonly class User {
    public string $username;
    public string $email;

    public function __construct(string $username, string $email) {
        $this->username = $username;
        $this->email = $email;
    }
}

$user = new User('John', 'johndoe@example.com');
$user->email = 'bob@example.com'; // ❌ Error: class is readonly
```

## Single Responsibility Principle (SRP)

When developing a class, SRP must be considered. As the name suggests, each class has a single responsibility and calls other classes/objects/functions if it needs to do or affect something else.

As previously mentioned in the [function guide](https://webwhales.teamwork.com/spaces/programmeren/page/5148-functions#one-purpose-single-responsibility-principle), here we can use the same example, logically in the context of a class.

> **ℹ️ Note:** Example: In a webshop system, when a new order is placed, an order is created in the system and a confirmation email is sent to the customer. For creating the order, there is a separate class, where the SRP of this class are the orders. For sending the mail is also a separate class created, when creating the order this class can be called to send the mail.

> **⚠️ Warning:** Notice: Implementing this principle creates many small classes, however, the classes should not be dependent on each other. With the above example, it should not be that the Mail class is only implementable within the OrderClass.

---

## Organize for change or Open-Close Principle (OCP)

Classes should be built with the idea that the application should be easily modified in the future. The main means of achieving this is by ensuring that code is not modified but extended.

For this purpose, for example, abstract classes can be used, with these the basic functionalities can be defined for all classes that extend on them. However, adjustments to the code do not impact the overall whole, reducing the chance of errors.

Read more about [Open-Close principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle).

```php
class ReportGenerator {
    public function generateReport(string $type): void {
        if ($type === 'PDF') {
            // Generate PDF report
        } elseif ($type === 'Excel') {
            // Generate Excel report
        }
    }
}

$reportGenerator = new ReportGenerator();
$reportGenerator->generateReport('PDF');
$reportGenerator->generateReport('Excel');

```

 

```php
interface ReportInterface {
    public function generate();
}

class PDFReport implements ReportInterface {
    public function generate() {
        echo "Generating PDF report\n";
    }
}

class ExcelReport implements ReportInterface {
    public function generate() {
        echo "Generating Excel report\n";
    }
}

class ReportGenerator {
    private $report;

    public function __construct(ReportInterface $report) {
        $this->report = $report;
    }

    public function generateReport() {
        $this->report->generate();
    }
}

$pdfReport = new PDFReport();
$excelReport = new ExcelReport();

$reportGenerator = new ReportGenerator($pdfReport);
$reportGenerator->generateReport();

$reportGenerator = new ReportGenerator($excelReport);
$reportGenerator->generateReport();

```

> **ℹ️ Note:** Example: If you have a class that handles functions concerning inputs, it could be that this was all put inside a class (i.e., all type of inputs that were needed). In case a new input is needed in the future, every switch, if statement, etc. must be checked and adjusted to make sure the new input works. Instead, you can also set up an (abstract) parent class that handles the general functions without depending on what type of input is involved. Then all other inputs can get their own class that extends on the parent. This way, a new input never affects the other inputs and your code is a lot easier to change.

