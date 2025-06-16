# Go Coding Conventions Guide

Welcome to our Go development team! This guide will help you write clean, idiomatic Go code that follows our team's conventions and Go community best practices.

## Table of Contents
1. [Naming Conventions](#naming-conventions)
2. [Struct and Interface Design](#struct-and-interface-design)
3. [Error Handling](#error-handling)
4. [General Code Style](#general-code-style)

---

## Naming Conventions

### Variables and Functions: camelCase

Use camelCase for variables and functions that are not exported (package-private).

✅ **Good:**
```go
var userName string
var isActive bool

func calculateTotal() int {
    return 0
}

func processUserData(userData []byte) error {
    return nil
}
```

❌ **Bad:**
```go
var user_name string        // snake_case - not Go style
var IsActive bool           // PascalCase for unexported - wrong
var USER_NAME string        // SCREAMING_SNAKE_CASE for variables - wrong

func Calculate_Total() int { // snake_case and exported when shouldn't be
    return 0
}
```

**Why follow this?** Go has established conventions that make code immediately recognizable to other Go developers. camelCase for unexported identifiers is the standard.

### Constants: SCREAMING_SNAKE_CASE or CamelCase

For package-level constants, use SCREAMING_SNAKE_CASE. For exported constants, you may use CamelCase.

✅ **Good:**
```go
const (
    MAX_RETRY_COUNT = 3
    DEFAULT_TIMEOUT = 30
    apiVersion      = "v1" // unexported, camelCase is fine
)

// Exported constants can use CamelCase
const (
    DefaultPort = 8080
    MaxUsers    = 1000
)
```

❌ **Bad:**
```go
const (
    maxRetryCount = 3    // Should be SCREAMING_SNAKE_CASE for clarity
    default_timeout = 30 // Mixing conventions
)
```

**Why follow this?** Constants should stand out visually in code. SCREAMING_SNAKE_CASE makes them immediately identifiable.

### Structs, Interfaces, Types: PascalCase

All custom types use PascalCase. Capitalization determines visibility.

✅ **Good:**
```go
// Exported types
type User struct {
    Name  string
    Email string
}

type UserRepository interface {
    GetUser(id int) (*User, error)
    SaveUser(user *User) error
}

// Unexported types
type internalConfig struct {
    apiKey string
    debug  bool
}
```

❌ **Bad:**
```go
type user struct {          // Should be User if exported
    Name  string
    Email string
}

type userRepository interface { // Should be UserRepository if exported
    GetUser(id int) (*user, error)
}

type User_Config struct {   // snake_case mixed with PascalCase
    ApiKey string
}
```

**Why follow this?** PascalCase is Go's convention for types, and capitalization controls package visibility.

### Exported vs Unexported Identifiers

Capitalization determines visibility across packages.

✅ **Good:**
```go
package user

// Exported (public) - starts with capital letter
type User struct {
    Name  string // exported field
    email string // unexported field
}

func NewUser(name, email string) *User {
    return &User{Name: name, email: email}
}

func (u *User) GetEmail() string {
    return u.email
}

// Unexported (private) - starts with lowercase
func validateEmail(email string) bool {
    return strings.Contains(email, "@")
}
```

❌ **Bad:**
```go
// Exporting everything unnecessarily
type user struct {          // Should be User if you want it exported
    Name  string
    Email string
}

// Or making everything unexported when it should be public
type User struct {
    name  string           // Should be Name if users need access
    email string
}
```

**Why follow this?** This controls your package's API surface. Export only what consumers need to use.

**Pros:** Clear API boundaries, encapsulation
**Cons:** Need to be thoughtful about what to expose

---

## Struct and Interface Design

### When to Use Pointers vs Values

Use pointers for large structs, when you need to modify the receiver, or when you want to avoid copying.

✅ **Good:**
```go
// Use pointer receiver for methods that modify state
type Counter struct {
    value int
}

func (c *Counter) Increment() {
    c.value++
}

func (c *Counter) Value() int {  // Consistent - use pointer even for read-only
    return c.value
}

// Use value receiver for small, immutable structs
type Point struct {
    X, Y int
}

func (p Point) String() string {
    return fmt.Sprintf("(%d,%d)", p.X, p.Y)
}
```

❌ **Bad:**
```go
// Mixing pointer and value receivers inconsistently
type Counter struct {
    value int
}

func (c *Counter) Increment() {
    c.value++
}

func (c Counter) Value() int {  // Inconsistent - should use pointer
    return c.value
}

// Using value receiver for large struct
type LargeStruct struct {
    data [1000]int
    name string
    // ... many more fields
}

func (ls LargeStruct) Process() {  // Will copy entire struct!
    // processing logic
}
```

**Why follow this?** Consistency in receiver types prevents confusion. Pointer receivers for large structs avoid expensive copying.

**Pros:** Performance, consistency, predictable behavior
**Cons:** Need to understand pointer semantics

### Embedding vs Composition

Prefer composition over embedding unless you truly want to promote methods.

✅ **Good:**
```go
// Composition - clear relationships
type Database struct {
    connection *sql.DB
    logger     *log.Logger
}

func (db *Database) Query(query string) (*sql.Rows, error) {
    db.logger.Printf("Executing query: %s", query)
    return db.connection.Query(query)
}

// Embedding when you want to promote behavior
type ReadOnlyDB struct {
    *Database
}

func (r *ReadOnlyDB) Insert(query string) error {
    return errors.New("read-only database")
}
```

❌ **Bad:**
```go
// Embedding when composition would be clearer
type UserService struct {
    *Database  // Unclear what methods are available
    *Logger    // Method name conflicts possible
    *Cache
}

// Now UserService has dozens of promoted methods that may not make sense
```

**Why follow this?** Composition makes dependencies explicit and relationships clear.

**Pros:** Clear interfaces, explicit dependencies
**Cons:** More verbose, need to write forwarding methods

### Avoiding Empty Interfaces

Avoid `interface{}` (or `any` in Go 1.18+) when possible. Use specific types or generics.

✅ **Good:**
```go
// Specific interface
type Serializer interface {
    Serialize() ([]byte, error)
}

func ProcessData(s Serializer) error {
    data, err := s.Serialize()
    // ... process data
    return err
}

// With Go 1.18+ generics
func ProcessSlice[T any](items []T, fn func(T) error) error {
    for _, item := range items {
        if err := fn(item); err != nil {
            return err
        }
    }
    return nil
}
```

❌ **Bad:**
```go
// Empty interface loses type safety
func ProcessData(data interface{}) error {
    // Need type assertions everywhere
    switch v := data.(type) {
    case string:
        // handle string
    case int:
        // handle int
    default:
        return fmt.Errorf("unsupported type: %T", v)
    }
    return nil
}

func ProcessAnything(items []interface{}) error {
    // Type assertions needed for every element
    for _, item := range items {
        // ... complex type switching
    }
    return nil
}
```

**Why follow this?** Type safety catches errors at compile time rather than runtime.

**Pros:** Compile-time safety, better documentation, IDE support
**Cons:** Less flexibility, more specific code

---

## Error Handling

### Return Error as Last Value

Always return error as the last parameter in multi-return functions.

✅ **Good:**
```go
func ReadUser(id int) (*User, error) {
    if id <= 0 {
        return nil, fmt.Errorf("invalid user id: %d", id)
    }
    
    user, err := db.GetUser(id)
    if err != nil {
        return nil, fmt.Errorf("failed to get user %d: %w", id, err)
    }
    
    return user, nil
}

func ProcessUsers(ids []int) ([]User, int, error) {
    var users []User
    processed := 0
    
    for _, id := range ids {
        user, err := ReadUser(id)
        if err != nil {
            return users, processed, err
        }
        users = append(users, *user)
        processed++
    }
    
    return users, processed, nil
}
```

❌ **Bad:**
```go
func ReadUser(id int) (error, *User) {  // Error should be last
    // implementation
}

func ProcessData() (error, string, int) {  // Error should be last
    // implementation
}

// Ignoring errors
func ReadUser(id int) *User {
    user, _ := db.GetUser(id)  // Ignoring error!
    return user
}
```

**Why follow this?** This is Go's standard convention. It makes error handling consistent across all Go code.

### Avoid Panic Except in Exceptional Situations

Use panic only for truly exceptional conditions that represent programmer errors.

✅ **Good:**
```go
func NewDatabase(connectionString string) (*Database, error) {
    if connectionString == "" {
        return nil, errors.New("connection string cannot be empty")
    }
    
    db, err := sql.Open("postgres", connectionString)
    if err != nil {
        return nil, fmt.Errorf("failed to open database: %w", err)
    }
    
    return &Database{db: db}, nil
}

// Panic only for programmer errors or initialization
func init() {
    if os.Getenv("REQUIRED_CONFIG") == "" {
        panic("REQUIRED_CONFIG environment variable must be set")
    }
}
```

❌ **Bad:**
```go
func ReadUser(id int) *User {
    user, err := db.GetUser(id)
    if err != nil {
        panic(err)  // Don't panic for recoverable errors!
    }
    return user
}

func ProcessFile(filename string) {
    file, err := os.Open(filename)
    if err != nil {
        panic("file not found")  // This should be returned as error
    }
    defer file.Close()
    // process file
}
```

**Why follow this?** Panics crash programs. Errors can be handled gracefully by callers.

**Pros:** Forces proper error handling, more robust applications
**Cons:** More verbose, need to handle errors at each level

### Use Wrapped Errors with fmt.Errorf and %w

Wrap errors to provide context while preserving the original error for inspection.

✅ **Good:**
```go
func SaveUser(user *User) error {
    if err := validateUser(user); err != nil {
        return fmt.Errorf("user validation failed: %w", err)
    }
    
    if err := db.Insert(user); err != nil {
        return fmt.Errorf("failed to save user %q: %w", user.Name, err)
    }
    
    return nil
}

func ProcessUserFile(filename string) error {
    users, err := loadUsersFromFile(filename)
    if err != nil {
        return fmt.Errorf("failed to load users from %q: %w", filename, err)
    }
    
    for _, user := range users {
        if err := SaveUser(&user); err != nil {
            return fmt.Errorf("failed to process user in file %q: %w", filename, err)
        }
    }
    
    return nil
}

// Error checking with errors.Is
func HandleError(err error) {
    if errors.Is(err, sql.ErrNoRows) {
        log.Println("No data found")
        return
    }
    log.Printf("Unexpected error: %v", err)
}
```

❌ **Bad:**
```go
func SaveUser(user *User) error {
    if err := validateUser(user); err != nil {
        return fmt.Errorf("user validation failed: %s", err.Error())  // Loses original error
    }
    
    if err := db.Insert(user); err != nil {
        return errors.New("database error")  // Loses context and original error
    }
    
    return nil
}

// Creating new errors instead of wrapping
func ProcessData() error {
    _, err := fetchData()
    if err != nil {
        return errors.New("data fetch failed")  // Original error lost
    }
    return nil
}
```

**Why follow this?** Error wrapping preserves error chains for debugging while adding context.

**Pros:** Better debugging, error inspection with errors.Is/As, context preservation
**Cons:** Longer error messages, need to understand error wrapping

---

## General Code Style

### Always Use gofmt or goimports

Format your code automatically to maintain consistency.

✅ **Good:**
```go
// Properly formatted with gofmt/goimports
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, World!")
    })
    
    log.Println("Server starting on :8080")
    if err := http.ListenAndServe(":8080", nil); err != nil {
        log.Fatal(err)
    }
}
```

❌ **Bad:**
```go
// Poorly formatted, inconsistent spacing
package main
import(
"fmt"
    "log"
"net/http"
)
func main(){
http.HandleFunc("/",func(w http.ResponseWriter,r *http.Request){
fmt.Fprintf(w,"Hello, World!")})
log.Println("Server starting on :8080")
if err:=http.ListenAndServe(":8080",nil);err!=nil{
log.Fatal(err)}}
```

**Why follow this?** Consistent formatting makes code readable and eliminates style debates.

**Pros:** Consistency, no formatting debates, automatic
**Cons:** Must set up tooling

### Avoid Deep Nesting; Use Early Return

Reduce cognitive load by handling error cases early and avoiding deep indentation.

✅ **Good:**
```go
func ProcessUser(userID int) error {
    if userID <= 0 {
        return errors.New("invalid user ID")
    }
    
    user, err := fetchUser(userID)
    if err != nil {
        return fmt.Errorf("failed to fetch user: %w", err)
    }
    
    if !user.IsActive {
        return errors.New("user is not active")
    }
    
    if err := validateUser(user); err != nil {
        return fmt.Errorf("user validation failed: %w", err)
    }
    
    return saveUser(user)
}

func FindActiveUsers(users []User) []User {
    var activeUsers []User
    
    for _, user := range users {
        if !user.IsActive {
            continue  // Early continue instead of nesting
        }
        
        if user.LastLogin.IsZero() {
            continue
        }
        
        activeUsers = append(activeUsers, user)
    }
    
    return activeUsers
}
```

❌ **Bad:**
```go
func ProcessUser(userID int) error {
    if userID > 0 {
        user, err := fetchUser(userID)
        if err == nil {
            if user.IsActive {
                if err := validateUser(user); err == nil {
                    return saveUser(user)
                } else {
                    return fmt.Errorf("user validation failed: %w", err)
                }
            } else {
                return errors.New("user is not active")
            }
        } else {
            return fmt.Errorf("failed to fetch user: %w", err)
        }
    } else {
        return errors.New("invalid user ID")
    }
}

func FindActiveUsers(users []User) []User {
    var activeUsers []User
    
    for _, user := range users {
        if user.IsActive {                    // Deep nesting
            if !user.LastLogin.IsZero() {
                activeUsers = append(activeUsers, user)
            }
        }
    }
    
    return activeUsers
}
```

**Why follow this?** Reduces cognitive load and makes the happy path clearer.

**Pros:** More readable, easier to follow logic, less mental overhead
**Cons:** May result in more return statements

### Favor Short, Clear Function Names and Logic

Write functions that do one thing well with clear, concise names.

✅ **Good:**
```go
func IsValidEmail(email string) bool {
    return strings.Contains(email, "@") && strings.Contains(email, ".")
}

func HashPassword(password string) (string, error) {
    if len(password) < 8 {
        return "", errors.New("password too short")
    }
    
    hash, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
    if err != nil {
        return "", fmt.Errorf("failed to hash password: %w", err)
    }
    
    return string(hash), nil
}

func LoadConfig() (*Config, error) {
    return &Config{
        Port:    getEnvInt("PORT", 8080),
        Host:    getEnvString("HOST", "localhost"),
        Debug:   getEnvBool("DEBUG", false),
    }, nil
}

func getEnvString(key, defaultValue string) string {
    if value := os.Getenv(key); value != "" {
        return value
    }
    return defaultValue
}
```

❌ **Bad:**
```go
func ValidateEmailAddressAndReturnBooleanIndicatingWhetherItIsValidOrNot(email string) bool {
    // Name is too verbose
    return strings.Contains(email, "@") && strings.Contains(email, ".")
}

func ProcessUserDataAndValidateAndSaveToDatabase(userData map[string]interface{}) error {
    // Function doing too many things
    
    // Validation logic
    email, ok := userData["email"].(string)
    if !ok || !strings.Contains(email, "@") {
        return errors.New("invalid email")
    }
    
    name, ok := userData["name"].(string)
    if !ok || len(name) < 2 {
        return errors.New("invalid name")
    }
    
    // Password hashing logic
    password, ok := userData["password"].(string)
    if !ok || len(password) < 8 {
        return errors.New("invalid password")
    }
    
    hash, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
    if err != nil {
        return err
    }
    
    // Database saving logic
    query := "INSERT INTO users (name, email, password_hash) VALUES (?, ?, ?)"
    _, err = db.Exec(query, name, email, string(hash))
    return err
}
```

**Why follow this?** Single responsibility makes functions easier to test, understand, and reuse.

**Pros:** Easier testing, better reusability, clearer intent
**Cons:** More functions to manage, need to find good boundaries

---

## Quick Reference Checklist

Before submitting code, ask yourself:

- [ ] Are my variable/function names camelCase (if unexported) or PascalCase (if exported)?
- [ ] Are my constants properly formatted (SCREAMING_SNAKE_CASE or CamelCase)?
- [ ] Do I return errors as the last parameter?
- [ ] Am I wrapping errors with context using `fmt.Errorf` and `%w`?
- [ ] Have I avoided deep nesting with early returns?
- [ ] Are my functions focused on a single responsibility?
- [ ] Did I run `gofmt` or `goimports` on my code?
- [ ] Am I using the right receiver types (pointer vs value) consistently?

## Additional Resources

- [Effective Go](https://golang.org/doc/effective_go.html) - Official Go documentation
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments) - Common review feedback
- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md) - Comprehensive style guide

Remember: These conventions exist to make our code consistent, readable, and maintainable. When in doubt, favor clarity and simplicity!
