# Tests

Automated testing includes different types of tests. Most projects that uses automated tests has one or more of the following types of tests :

- **Unit Tests:** Testing an isolated piece of code, often 1 class or 1 function.
- **Feature Tests (or Component Tests):** The combined testing of pieces of code, often multiple classes or multiple functions from different classes that work together. Feature tests can also test the combination of backend and frontend code.
- **Integration Tests (or End-to-end Tests):** Testing code that interacts with external services. For example, code that integrates with an API from an external system.
- **Browser Tests:** Testing the application from a browser perspective with capabilities to test user interaction, user flows et cetera.

The first three types of tests are often incorrectly bundled by the term “Unit Testing”. The term “Automated Testing” describes the overarching concept better.

## Naming conventions

### Class names

Test classes are named after the topic they contain tests about. Test classes should be split up when they are covering multiple topics.

 **Correct**

```php
class AddToCartTest extends TestCase
{
    ...
}
```

 **Wrong**

```php
class CartTest extends TestCase
{
    ...
}
```

### Method names

Test method names should briefly describe the expected behavior and, if applicable - in most cases the unhappy path, the conditions.

Method names are prefixed with `test_` and written in *snake\_case* style.

 **Correct**

```php
class AddToCartTest extends TestCase
{
    public function test_products_can_be_added()
    {
        ...
    }

    public function test_products_without_stock_cannot_be_added()
    {
        ...
    }
}
```

 **Wrong**

```php
class AddToCartTest extends TestCase
{
    public function test_a_user_can_add_a_product_to_his_cart()
    {
        ...
    }

    public function test_a_user_can_not_add_a_product_to_his_cart_when_it_is_out_of_stock()
    {
        ...
    }
}
```

## Data Providers

Multiple tests covering the same part of the application should be bundled using data providers. Data providers using doc blocks are preferred. Data providers using methods can be used when more complex data is required or when the same provided data is needed for multiple tests.

When using data provider methods, method names are suffixed with `Prodider` and written in *camelCase* style.

 **Example**

```php
class AddToCartTest extends TestCase
{
    /**
     * @testWith [0]
     *           [-1]
     *           [-99999]
     */
    public function test_products_without_stock_cannot_be_added(int $stockLevel)
    {
        ...
    }

    // Or using data provider methods, when data is required multiple
    // times or when the provided data is more complex.

    /**
     * @dataProvider negativeStockLevelProvider
     */
    public function test_products_without_stock_cannot_be_added(int $stockLevel)
    {
        ...
    }

    public static function negativeStockLevelProvider(): array
    {
      ...
    }
}
```

## Ordering methods

Within test classes, methods are ordered as follows:

1. Setup and teardown
2. Test methods
    In logical order - happy path first
3. Helper methods
4. Data providers

 **Example**

```php
class AddToCartTest extends TestCase
{
    public function setUp: void
    {
      ...
    }

    public function tearDown: void
    {
      ...
    }

    public function test_products_can_be_added()
    {
      ...
    }

    /**
     * @dataProvider negativeStockLevelProvider
     */
    public function test_products_without_stock_cannot_be_added(int $stockLevel)
    {
        ...
    }

    private function prepareCart()
    {
      ...
    }

    public static function negativeStockLevelProvider(): array
    {
      ...
    }
}
```

## Avoid properties

Usage of properties should be avoided whenever possible since they easily cause memory leaks. When you really need a property, make sure to unset it in the `tearDown` method.

