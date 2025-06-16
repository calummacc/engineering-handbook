# TypeScript Coding Conventions Guide 
Welcome to our TypeScript coding conventions! This guide will help you write clean, maintainable, and type-safe code that follows our team's standards.

## 1. Naming Conventions

### Variables and Functions: camelCase

**Rule:** Use camelCase for variables, functions, and methods.

```typescript
// ✅ Good
const userName = 'john_doe';
const totalPrice = 199.99;

function calculateTax(amount: number): number {
  return amount * 0.08;
}

// ❌ Bad
const user_name = 'john_doe';      // snake_case
const TotalPrice = 199.99;         // PascalCase
const TOTAL_PRICE = 199.99;        // SCREAMING_SNAKE_CASE (unless constant)

function CalculateTax(amount: number): number {  // PascalCase
  return amount * 0.08;
}
```

**✅ Pros:**
- Consistent with JavaScript conventions
- Easy to read and widely adopted
- Supported by most linters and formatters

**❌ Cons:**
- None significant - this is the established standard

**Why follow this:** camelCase is the JavaScript/TypeScript standard. Consistency across the codebase makes it easier for team members to read and maintain code.

### Classes and Interfaces: PascalCase

**Rule:** Use PascalCase for classes, interfaces, types, and enums.

```typescript
// ✅ Good
class UserService {
  private users: User[] = [];
}

interface ApiResponse {
  data: unknown;
  status: number;
}

type DatabaseConnection = {
  host: string;
  port: number;
};

enum OrderStatus {
  Pending = 'pending',
  Shipped = 'shipped',
  Delivered = 'delivered'
}

// ❌ Bad
class userService { }           // camelCase
interface apiResponse { }       // camelCase
type databaseConnection = { };  // camelCase
enum orderStatus { }           // camelCase
```

**✅ Pros:**
- Clear distinction between types/classes and variables/functions
- Follows TypeScript and other language conventions
- Makes code more readable

**❌ Cons:**
- None - this is the established standard

**Why follow this:** PascalCase for types and classes is a universal convention that helps distinguish between different kinds of identifiers at a glance.

### Constants: SCREAMING_SNAKE_CASE

**Rule:** Use SCREAMING_SNAKE_CASE for module-level constants and configuration values.

```typescript
// ✅ Good
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_TIMEOUT_MS = 5000;

// ✅ Also acceptable for local constants that won't change
const TAX_RATE = 0.08;

// ❌ Bad
const apiBaseUrl = 'https://api.example.com';     // camelCase for constants
const maxRetryAttempts = 3;                       // camelCase for constants
```

**✅ Pros:**
- Immediately identifies unchanging values
- Prevents accidental modification
- Standard across many programming languages

**❌ Cons:**
- Can be verbose
- Not always necessary for simple local constants

**Why follow this:** Makes it clear which values are meant to be constant configuration, helping prevent bugs and improving code readability.

### Interface Naming: No "I" Prefix

**Rule:** Don't prefix interfaces with "I". Use descriptive names instead.

```typescript
// ✅ Good
interface User {
  id: string;
  name: string;
  email: string;
}

interface PaymentProcessor {
  processPayment(amount: number): Promise<boolean>;
}

// ❌ Bad
interface IUser {           // Unnecessary "I" prefix
  id: string;
  name: string;
  email: string;
}

interface IPaymentProcessor {  // Unnecessary "I" prefix
  processPayment(amount: number): Promise<boolean>;
}
```

**✅ Pros:**
- Cleaner, more modern TypeScript style
- Focuses on what the interface represents, not its technical nature
- Recommended by TypeScript team

**❌ Cons:**
- Some developers from C# backgrounds might prefer "I" prefix
- Slightly less explicit about being an interface

**Why follow this:** Modern TypeScript convention. The "I" prefix is considered outdated and adds unnecessary noise to interface names.

## 2. Type vs Interface Guidelines

### Use Interface for Object Shapes

**Rule:** Prefer `interface` when defining the shape of objects, especially when you might need to extend them later.

```typescript
// ✅ Good - Use interface for object shapes
interface User {
  id: string;
  name: string;
  email: string;
}

interface AdminUser extends User {
  permissions: string[];
}

// ✅ Also good - Interface can be augmented
interface ApiClient {
  get(url: string): Promise<Response>;
}

// Later in another file
interface ApiClient {
  post(url: string, data: unknown): Promise<Response>;
}

// ❌ Less ideal - Type for simple object shapes
type User = {
  id: string;
  name: string;
  email: string;
};
```

**✅ Pros:**
- Can be extended and merged
- More flexible for future modifications
- Better for defining contracts

**❌ Cons:**
- Slightly more verbose than type aliases
- Cannot represent unions or computed types

**Why follow this:** Interfaces are more extensible and work better with TypeScript's declaration merging, making them ideal for object shapes that might evolve.

### Use Type for Complex Types

**Rule:** Use `type` for unions, intersections, computed types, and complex type operations.

```typescript
// ✅ Good - Use type for unions
type Theme = 'light' | 'dark' | 'auto';
type Status = 'loading' | 'success' | 'error';

// ✅ Good - Use type for intersections
type UserWithProfile = User & Profile;

// ✅ Good - Use type for computed/mapped types
type PartialUser = Partial<User>;
type UserKeys = keyof User;

// ✅ Good - Use type for function signatures
type EventHandler = (event: Event) => void;
type AsyncFunction<T> = () => Promise<T>;

// ❌ Bad - Interface cannot represent unions
interface Theme {  // This won't work for 'light' | 'dark' | 'auto'
  // ...
}
```

**✅ Pros:**
- Can represent complex type operations
- More concise for simple type aliases
- Required for unions and intersections

**❌ Cons:**
- Cannot be extended with `extends` keyword
- Cannot be augmented/merged

**Why follow this:** Types are more powerful for complex type operations, while interfaces excel at defining object contracts.

## 3. Type Safety Best Practices

### Avoid `any` - Use Specific Types

**Rule:** Never use `any` unless absolutely necessary. Always try to define specific types.

```typescript
// ✅ Good - Specific types
interface ApiResponse {
  data: User[];
  status: number;
  message: string;
}

function processResponse(response: ApiResponse): User[] {
  return response.data;
}

// ✅ Good - Using generics for flexibility
function processApiResponse<T>(response: { data: T; status: number }): T {
  return response.data;
}

// ❌ Bad - Using any loses all type safety
function processResponse(response: any): any {
  return response.data;  // No type checking, no IntelliSense
}
```

**✅ Pros:**
- Full type checking and IntelliSense support
- Catches errors at compile time
- Self-documenting code

**❌ Cons:**
- Requires more initial effort to define types
- May need to learn advanced TypeScript features

**Why follow this:** `any` defeats the purpose of using TypeScript. Specific types catch bugs early and make code more maintainable.

### Use `unknown` for Uncertain Types

**Rule:** When you truly don't know the type of data, use `unknown` instead of `any`.

```typescript
// ✅ Good - Using unknown forces type checking
function parseJsonSafely(jsonString: string): unknown {
  try {
    return JSON.parse(jsonString);
  } catch {
    return null;
  }
}

function processUnknownData(data: unknown): string {
  // Must check type before using
  if (typeof data === 'string') {
    return data.toUpperCase();
  }
  if (typeof data === 'object' && data !== null && 'message' in data) {
    return String((data as { message: unknown }).message);
  }
  return 'Invalid data';
}

// ❌ Bad - any allows unsafe operations
function parseJsonUnsafely(jsonString: string): any {
  return JSON.parse(jsonString);
}

function processAnyData(data: any): string {
  return data.toUpperCase();  // Runtime error if data isn't a string!
}
```

**✅ Pros:**
- Maintains type safety
- Forces explicit type checking
- Prevents runtime errors

**❌ Cons:**
- Requires type guards and additional checks
- More verbose than `any`

**Why follow this:** `unknown` is type-safe `any`. It forces you to check types before using values, preventing runtime errors.

### Always Specify Function Return Types

**Rule:** Explicitly declare return types for all functions, especially public APIs.

```typescript
// ✅ Good - Explicit return types
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

async function fetchUser(id: string): Promise<User | null> {
  try {
    const response = await fetch(`/api/users/${id}`);
    return await response.json();
  } catch {
    return null;
  }
}

function processItems(items: Item[]): { total: number; count: number } {
  return {
    total: calculateTotal(items),
    count: items.length
  };
}

// ❌ Bad - Implicit return types
function calculateTotal(items: Item[]) {  // Return type inferred as number
  return items.reduce((sum, item) => sum + item.price, 0);
}

async function fetchUser(id: string) {    // Return type inferred as Promise<any>
  const response = await fetch(`/api/users/${id}`);
  return await response.json();
}
```

**✅ Pros:**
- Makes function contracts explicit
- Catches return type errors early
- Improves code documentation
- Better IntelliSense for callers

**❌ Cons:**
- Slightly more verbose
- Need to update if return type changes

**Why follow this:** Explicit return types serve as documentation and catch errors where functions might return unexpected types.

## 4. General Code Style Best Practices

### Use `const` Over `let`

**Rule:** Use `const` by default, only use `let` when you need to reassign the variable.

```typescript
// ✅ Good - Using const for values that don't change
const users = [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }];
const apiUrl = 'https://api.example.com';

function processUsers(): void {
  const filteredUsers = users.filter(user => user.id > 0);
  const userCount = filteredUsers.length;
  
  // ✅ Good - let only when reassignment is needed
  let message = 'Processing users...';
  if (userCount === 0) {
    message = 'No users found';
  }
  
  console.log(message);
}

// ❌ Bad - Using let unnecessarily
let users = [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }];  // Never reassigned
let apiUrl = 'https://api.example.com';                           // Never reassigned

function processUsers(): void {
  let filteredUsers = users.filter(user => user.id > 0);         // Never reassigned
  let userCount = filteredUsers.length;                          // Never reassigned
}
```

**✅ Pros:**
- Prevents accidental reassignment
- Makes code intent clearer
- Helps catch bugs early
- Indicates immutable bindings

**❌ Cons:**
- Need to think about whether reassignment is needed
- Must change to `let` if requirements change

**Why follow this:** `const` makes code more predictable and prevents a common source of bugs (accidental reassignment).

### Avoid Deep Nesting

**Rule:** Keep nesting levels minimal. Use early returns, guard clauses, and extracted functions.

```typescript
// ✅ Good - Early returns and guard clauses
function processUser(user: User | null): string {
  if (!user) {
    return 'User not found';
  }
  
  if (!user.email) {
    return 'User email missing';
  }
  
  if (!isValidEmail(user.email)) {
    return 'Invalid email format';
  }
  
  return `Processing user: ${user.name}`;
}

// ✅ Good - Extract complex logic to separate functions
function validateOrder(order: Order): ValidationResult {
  const userValidation = validateUser(order.user);
  if (!userValidation.isValid) {
    return userValidation;
  }
  
  const itemsValidation = validateItems(order.items);
  if (!itemsValidation.isValid) {
    return itemsValidation;
  }
  
  return { isValid: true, message: 'Order is valid' };
}

// ❌ Bad - Deep nesting
function processUser(user: User | null): string {
  if (user) {
    if (user.email) {
      if (isValidEmail(user.email)) {
        if (user.isActive) {
          if (user.permissions.length > 0) {
            return `Processing active user: ${user.name}`;
          } else {
            return 'User has no permissions';
          }
        } else {
          return 'User is not active';
        }
      } else {
        return 'Invalid email format';
      }
    } else {
      return 'User email missing';
    }
  } else {
    return 'User not found';
  }
}
```

**✅ Pros:**
- Easier to read and understand
- Reduces cognitive load
- Easier to test individual conditions
- Less prone to logic errors

**❌ Cons:**
- May result in more lines of code
- Multiple return points (some consider this a con)

**Why follow this:** Deep nesting makes code hard to read and reason about. Flat code structure is easier to understand and maintain.

### Prefer Destructuring and Spread Syntax

**Rule:** Use destructuring for extracting values and spread syntax for copying/merging objects and arrays.

```typescript
// ✅ Good - Object destructuring
interface User {
  id: string;
  name: string;
  email: string;
  settings: UserSettings;
}

function displayUser({ name, email }: User): string {
  return `${name} (${email})`;
}

function processUser(user: User): void {
  const { id, name, settings } = user;
  console.log(`Processing user ${id}: ${name}`);
  updateUserSettings(settings);
}

// ✅ Good - Array destructuring
const coordinates = [10, 20, 30];
const [x, y, z] = coordinates;

// ✅ Good - Spread syntax for copying/merging
const originalUser: User = { id: '1', name: 'Alice', email: 'alice@example.com', settings: {} };
const updatedUser: User = {
  ...originalUser,
  name: 'Alice Smith',
  settings: { ...originalUser.settings, theme: 'dark' }
};

const numbers = [1, 2, 3];
const moreNumbers = [...numbers, 4, 5, 6];

// ❌ Bad - Manual property access
function displayUser(user: User): string {
  return `${user.name} (${user.email})`;  // Could use destructuring
}

function processUser(user: User): void {
  const id = user.id;          // Verbose compared to destructuring
  const name = user.name;
  const settings = user.settings;
  console.log(`Processing user ${id}: ${name}`);
}

// ❌ Bad - Manual copying
const updatedUser: User = {
  id: originalUser.id,
  name: 'Alice Smith',         // Manually copying each property
  email: originalUser.email,
  settings: originalUser.settings
};
```

**✅ Pros:**
- More concise and readable
- Reduces repetitive code
- Makes data flow more explicit
- Helps prevent mutation bugs

**❌ Cons:**
- Can be confusing for beginners
- May make debugging slightly harder in some cases
- Not always more readable for simple cases

**Why follow this:** Destructuring and spread syntax are modern JavaScript/TypeScript features that make code more concise and help prevent common bugs related to object mutation.

## Additional Tips for Success

### 1. Enable Strict Mode
Always use TypeScript's strict mode in your `tsconfig.json`:

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true
  }
}
```

### 2. Use Meaningful Names
Choose descriptive names that explain what the code does:

```typescript
// ✅ Good
const activeUsersCount = users.filter(user => user.isActive).length;
const isEmailValid = validateEmail(email);

// ❌ Bad
const count = users.filter(u => u.active).length;
const valid = check(email);
```

### 3. Write Self-Documenting Code
Strive for code that explains itself:

```typescript
// ✅ Good - Clear intent
function canUserAccessResource(user: User, resource: Resource): boolean {
  return user.permissions.includes(resource.requiredPermission) && 
         user.isActive && 
         !resource.isRestricted;
}

// ❌ Bad - Unclear logic
function check(u: User, r: Resource): boolean {
  return u.perms.includes(r.perm) && u.active && !r.restricted;
}
```

Remember: These conventions exist to make our code more readable, maintainable, and less prone to bugs. When in doubt, prioritize clarity and consistency over brevity. Happy coding!
