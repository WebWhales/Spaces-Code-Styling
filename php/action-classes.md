# Action classes

One way to isolate code in order to work with the DRY and Single Responsibility principles is to work with action classes.

Action classes should have one purpose or goal. They can contain multiple variations of an action, but they should never serve more than one goal.

Action classes generally have one public method `handle()` to achieve the goal of the action. However, action classes can have more public methods if they contain more actions that serve the same purpose which would cause duplicated code when split into multiple action classes.

 **Example**

```php
class ImportProductFromXmlAction
{
    public function handle(string $filePath): Product
    {
        // Read XML file and import product into the database from XML data
    }
}

class ControllerOrCommandClass
{
    public function controllerOrCommandMethod()
    {
        $importedProduct = (new ImportProductFromXmlAction)->handle(
            $this->getXmlPath()
        );

        // Do something with the imported
    }
}
```

 **Example with more than one handle method**

```php
class ImportProductAction
{
    public function handleFromCsv(string $filePath): Product
    {
        // Read CSV file and import product into the database from CSV data

        return $this->importProduct($ptoductData);
    }

    public function handleFromXml(string $filePath): Product
    {
        // Read XML file and import product into the database from XML data

        return $this->importProduct($ptoductData);
    }

    private function importProduct(array $data): Product
    {
        // Use an internally structured array to insert the product
    }
}
```

> **ℹ️ Note:** Depending on the size of the action class, multiple action classes using a trait to insert the product would also be a good solution here.

## Must see video reference

https://www.youtube.com/watch?v=0Rq-yHAwYjQ&t=1699

