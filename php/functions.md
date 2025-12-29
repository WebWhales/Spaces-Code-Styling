# Functions

When writing functions in any language, there are always best practices. Below are several examples that are good to adhere to. During the article also mention the technical name, should you be interested in more details, you can search with the appropriate terminology.

### **Small functions**

Keeping functions small makes them easier to understand. Functions can also be reused better this way; when a function executes a lot of code, it is limited to more specific situations.

There are many opinions regarding how long a function should be at most. A 60-line function may be easier to understand than a 5-line function (again, this depends on how complex and clear the content is). However, it is good to aim for manageable functions.

Usually, if a function is too large, there is a good chance that the function breaks with the Single Responsibility Principle.

---

### **One purpose (Single Responsibility Principle)**

A function should have a responsibility. When a function performs multiple tasks, it is better to split these functions whenever possible.

 **Wrong:**

```php
function save($request) {
    // Validate the request
    $validatedData = $request->validate([
        'name' => 'required|max:255',
        'email' => 'required|email|unique:users,email',
        'password' => 'required|min:6',
    ]);

    // Create the user
    $user = User::create([
        'name' => $validatedData['name'],
        'email' => $validatedData['email'],
        'password' => bcrypt($validatedData['password']),
    ]);

    // Send a welcome email
    Mail::to($user->email)->send(new WelcomeEmail($user));

    // Return a response
    return true;
}
```

  **Correct:**

```php
function save($request) {
    $data = validate($request);

    create($data);

    return true;
}

function validate($request) {
    return $request->validate([
        'name' => 'required',
        'email' => 'required|email',
        'password' => 'required|min:6',
    ]);
}

function create($data) {
    $user = User::create([
        'name' => $data['name'],
        'email' => $data['email'],
        'password' => bcrypt($data['password']),
    ]);

    sendWelcomeEmail($user);
}

function sendWelcomeEmail(User $user) {
    Mail::to($user->email)->send(new WelcomeEmail($user));
}
```

---

### **Few arguments** 

It is confusing to pass many arguments to a function. It is often better to use an object in this case.

 **Wrong:**

```php
function addProductAttributes(array &$productData, Product $product, array $fields, array $settings, bool $variantDetected = false): void
{
    // Do something.
}
```

 **Correct:**

```php
function addProductAttributes(Product $product): void
{
    // Do something.
}
```

---

### **Duplicated code (DRY: Don't Repeat Yourself)**

Try to reuse existing functions as much as possible. If you need a large chunk of an existing function, but you cannot use it to its full potential, try rewriting the existing function. The function can then be used for more situations.

 **Wrong:**

```php
function getUser(int $userId):string {
    try {
        $user = findUserById($userId);
        if (!$user) {
            return json_encode(['error' => 'User not found', 'status' => 404]);
        }
        return json_encode(['data' => $user, 'status' => 200]);
    } catch (Exception $e) {
        return json_encode(['error' => $e->getMessage(), 'status' => 500]);
    }
}

function deleteUser(int $userId):string {
    try {
        $result = deleteUserById($userId);
        if (!$result) {
            return json_encode(['error' => 'Failed to delete user', 'status' => 404]);
        }
        return json_encode(['message' => 'User deleted successfully', 'status' => 200]);
    } catch (Exception $e) {
        return json_encode(['error' => $e->getMessage(), 'status' => 500]);
    }
}
```

 **Correct:**

```php
function apiResponse(mixed $data, string $message = null, int $status = 200):string {
    $response = ['status' => $status];
    
    if ($data instanceof Exception) {
        $response['error'] = $data->getMessage();
        // Assuming all exceptions lead to a server error
        $response['status'] = 500;
    } else if ($data === null) {
        $response['error'] = 'Resource not found';
        $response['status'] = 404;
    } else {
        $response['data'] = $data;
        if ($message) {
            $response['message'] = $message;
        }
    }
    
    return json_encode($response);
}

function getUser(int $userId):string {
    try {
        $user = findUserById($userId);
        return apiResponse($user);
    } catch (Exception $e) {
        return apiResponse($e);
    }
}

function deleteUser(int $userId):string {
    try {
        $result = deleteUserById($userId);
        if (!$result) {
            return apiResponse(null);
        }
        return apiResponse(null, 'User deleted successfully');
    } catch (Exception $e) {
        return apiResponse($e);
    }
}
```

---

### **Extracting**

Functions should be partitioned among classes/traits/different files as much as possible. Here we must take into account the principles of Single Responsibility and Separation of Concerns.

This is an example to separate some classes:

 **Wrong:**

```php
class UserManager {
    public function createUser($data) {
        // Add user to database
    }

    public function authenticateUser($username, $password) {
        // Check user credentials
    }

    public function sendEmail($user, $message) {
        // Send an email to the user
    }
}
```

 **Correct:**

```php
class UserAuth {
    public function authenticateUser($username, $password) {
        // Check user credentials
    }
}

class UserManagement {
    public function createUser($data) {
        // Add user to database
    }
}

class NotificationManager {
    public function sendEmail($user, $message) {
        // Send an email to the user
    }
}
```

Using traits for shared functions:

 **Wrong:**

```php
class ProductManager {
    private function log($message) {
        echo "Log entry: $message";
    }

    public function updateInventory($product, $quantity) {
        $this->log("Updating inventory for $product");
        // update logic
    }
}

class OrderManager {
    private function log($message) {
        echo "Log entry: $message";
    }

    public function processOrder($order) {
        $this->log("Processing order $order");
        // processing logic
    }
}
```

 **Correct:**

```php
trait Logger {
    public function log($message) {
        echo "Log entry: $message";
    }
}

class ProductManager {
    use Logger;

    public function updateInventory($product, $quantity) {
        $this->log("Updating inventory for $product");
        // update logic
    }
}

class OrderManager {
    use Logger;

    public function processOrder($order) {
        $this->log("Processing order $order");
        // processing logic
    }
}
```

 **Wrong:**

```php
public function updateProductStockAndCustomerAddressUponPurchase():void {}
```

 **Correct:**

```php
namespace Customer\Actions;

class Purchase extends \SomeClass {
	public function handle( Customer $customer, int $orderId ):void {
		if ( !empty( $order = Order::getInstance()->getOrder( $orderId ) ) ) {
			foreach ( $order->products as $product ) {
				Product::getInstance()->updateProductStock( $order );
			}

			$customer->updateAddressByOrder( $order );
		}
	}
}
```

---

### **Clear and correct function names**

The use of clear and descriptive function is very important. The intent of a function should be clear just by reading the name. The more specific a function becomes the more elaborate the name is described. The construction of a function name is important, the correct way for this is by combining a verb followed by a noun, for example:

 **Wrong:**

```php
public function customerGetOrders():array {}
```

 **Correct:**

```php
public function getCustomerOrders():array {}
```

#### **Boolean return functions**

When a function returns a boolean, the function generally starts with "is" or "has" for example:

```php
public function isCustomerOnline():bool {
    // Do something.
}

public function hasOrders():bool {
    // Do something.
}
```

---

### **Type hinting / Type declaration**

The function arguments and the return value must be provided with a type.

Example:

```php
public function updateCustomerAccount( User $user, array $formData = [] ):bool {
    // Do something.
} 
```

