# Hướng Dẫn Quy Ước Lập Trình Go

Chào mừng bạn đến với đội ngũ phát triển Go của chúng tôi! Hướng dẫn này sẽ giúp bạn viết code Go sạch sẽ, dễ hiểu và tuân thủ theo các quy ước của team cũng như cộng đồng Go.

## Mục Lục
1. [Quy Ước Đặt Tên](#quy-ước-đặt-tên)
2. [Thiết Kế Struct và Interface](#thiết-kế-struct-và-interface)
3. [Xử Lý Lỗi](#xử-lý-lỗi)
4. [Phong Cách Code Chung](#phong-cách-code-chung)

---

## Quy Ước Đặt Tên

### Biến và Hàm: camelCase

Sử dụng camelCase cho các biến và hàm không được export (riêng tư trong package).

✅ **Tốt:**
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

❌ **Tệ:**
```go
var user_name string        // snake_case - không phải phong cách Go
var IsActive bool           // PascalCase cho unexported - sai
var USER_NAME string        // SCREAMING_SNAKE_CASE cho biến - sai

func Calculate_Total() int { // snake_case và exported khi không cần thiết
    return 0
}
```

**Tại sao nên tuân theo?** Go có các quy ước đã được thiết lập giúp code dễ nhận biết ngay với các developer Go khác. camelCase cho identifiers không được export là chuẩn.

### Hằng số: SCREAMING_SNAKE_CASE hoặc CamelCase

Đối với hằng số cấp package, sử dụng SCREAMING_SNAKE_CASE. Đối với hằng số được export, bạn có thể dùng CamelCase.

✅ **Tốt:**
```go
const (
    MAX_RETRY_COUNT = 3
    DEFAULT_TIMEOUT = 30
    apiVersion      = "v1" // unexported, camelCase cũng được
)

// Hằng số exported có thể dùng CamelCase
const (
    DefaultPort = 8080
    MaxUsers    = 1000
)
```

❌ **Tệ:**
```go
const (
    maxRetryCount = 3    // Nên là SCREAMING_SNAKE_CASE để rõ ràng
    default_timeout = 30 // Trộn lẫn các quy ước
)
```

**Tại sao nên tuân theo?** Hằng số nên nổi bật trong code. SCREAMING_SNAKE_CASE giúp nhận biết chúng ngay lập tức.

### Struct, Interface, Type: PascalCase

Tất cả các type tùy chỉnh đều sử dụng PascalCase. Viết hoa quyết định khả năng hiển thị.

✅ **Tốt:**
```go
// Type được export
type User struct {
    Name  string
    Email string
}

type UserRepository interface {
    GetUser(id int) (*User, error)
    SaveUser(user *User) error
}

// Type không được export
type internalConfig struct {
    apiKey string
    debug  bool
}
```

❌ **Tệ:**
```go
type user struct {          // Nên là User nếu được export
    Name  string
    Email string
}

type userRepository interface { // Nên là UserRepository nếu được export
    GetUser(id int) (*user, error)
}

type User_Config struct {   // snake_case trộn với PascalCase
    ApiKey string
}
```

**Tại sao nên tuân theo?** PascalCase là quy ước của Go cho các type, và viết hoa kiểm soát khả năng hiển thị của package.

### Exported vs Unexported Identifiers

Viết hoa quyết định khả năng hiển thị giữa các package.

✅ **Tốt:**
```go
package user

// Exported (public) - bắt đầu bằng chữ hoa
type User struct {
    Name  string // field được export
    email string // field không được export
}

func NewUser(name, email string) *User {
    return &User{Name: name, email: email}
}

func (u *User) GetEmail() string {
    return u.email
}

// Unexported (private) - bắt đầu bằng chữ thường
func validateEmail(email string) bool {
    return strings.Contains(email, "@")
}
```

❌ **Tệ:**
```go
// Export mọi thứ một cách không cần thiết
type user struct {          // Nên là User nếu bạn muốn export
    Name  string
    Email string
}

// Hoặc làm mọi thứ unexported khi nên là public
type User struct {
    name  string           // Nên là Name nếu người dùng cần truy cập
    email string
}
```

**Tại sao nên tuân theo?** Điều này kiểm soát API surface của package bạn. Chỉ export những gì người dùng cần sử dụng.

**Ưu điểm:** Ranh giới API rõ ràng, encapsulation
**Nhược điểm:** Cần suy nghĩ kỹ về những gì cần expose

---

## Thiết Kế Struct và Interface

### Khi Nào Sử Dụng Pointer vs Value

Sử dụng pointer cho struct lớn, khi bạn cần modify receiver, hoặc khi muốn tránh copy.

✅ **Tốt:**
```go
// Sử dụng pointer receiver cho method modify state
type Counter struct {
    value int
}

func (c *Counter) Increment() {
    c.value++
}

func (c *Counter) Value() int {  // Nhất quán - dùng pointer cả khi read-only
    return c.value
}

// Sử dụng value receiver cho struct nhỏ, immutable
type Point struct {
    X, Y int
}

func (p Point) String() string {
    return fmt.Sprintf("(%d,%d)", p.X, p.Y)
}
```

❌ **Tệ:**
```go
// Trộn lẫn pointer và value receiver không nhất quán
type Counter struct {
    value int
}

func (c *Counter) Increment() {
    c.value++
}

func (c Counter) Value() int {  // Không nhất quán - nên dùng pointer
    return c.value
}

// Sử dụng value receiver cho struct lớn
type LargeStruct struct {
    data [1000]int
    name string
    // ... nhiều field khác
}

func (ls LargeStruct) Process() {  // Sẽ copy toàn bộ struct!
    // logic xử lý
}
```

**Tại sao nên tuân theo?** Tính nhất quán trong receiver type tránh nhầm lẫn. Pointer receiver cho struct lớn tránh copy tốn kém.

**Ưu điểm:** Hiệu suất, nhất quán, hành vi dự đoán được
**Nhược điểm:** Cần hiểu về pointer semantics

### Embedding vs Composition

Ưu tiên composition hơn embedding trừ khi bạn thực sự muốn promote method.

✅ **Tốt:**
```go
// Composition - mối quan hệ rõ ràng
type Database struct {
    connection *sql.DB
    logger     *log.Logger
}

func (db *Database) Query(query string) (*sql.Rows, error) {
    db.logger.Printf("Executing query: %s", query)
    return db.connection.Query(query)
}

// Embedding khi bạn muốn promote behavior
type ReadOnlyDB struct {
    *Database
}

func (r *ReadOnlyDB) Insert(query string) error {
    return errors.New("read-only database")
}
```

❌ **Tệ:**
```go
// Embedding khi composition sẽ rõ ràng hơn
type UserService struct {
    *Database  // Không rõ method nào có sẵn
    *Logger    // Có thể xung đột tên method
    *Cache
}

// Bây giờ UserService có hàng chục promoted method có thể không có ý nghĩa
```

**Tại sao nên tuân theo?** Composition làm cho dependency rõ ràng và mối quan hệ rõ ràng.

**Ưu điểm:** Interface rõ ràng, dependency rõ ràng
**Nhược điểm:** Dài dòng hơn, cần viết forwarding method

### Tránh Empty Interface

Tránh `interface{}` (hoặc `any` trong Go 1.18+) khi có thể. Sử dụng type cụ thể hoặc generic.

✅ **Tốt:**
```go
// Interface cụ thể
type Serializer interface {
    Serialize() ([]byte, error)
}

func ProcessData(s Serializer) error {
    data, err := s.Serialize()
    // ... xử lý data
    return err
}

// Với Go 1.18+ generic
func ProcessSlice[T any](items []T, fn func(T) error) error {
    for _, item := range items {
        if err := fn(item); err != nil {
            return err
        }
    }
    return nil
}
```

❌ **Tệ:**
```go
// Empty interface mất type safety
func ProcessData(data interface{}) error {
    // Cần type assertion ở khắp nơi
    switch v := data.(type) {
    case string:
        // xử lý string
    case int:
        // xử lý int
    default:
        return fmt.Errorf("unsupported type: %T", v)
    }
    return nil
}

func ProcessAnything(items []interface{}) error {
    // Type assertion cần thiết cho mọi element
    for _, item := range items {
        // ... type switching phức tạp
    }
    return nil
}
```

**Tại sao nên tuân theo?** Type safety bắt lỗi tại compile time thay vì runtime.

**Ưu điểm:** An toàn compile-time, documentation tốt hơn, hỗ trợ IDE
**Nhược điểm:** Ít linh hoạt hơn, code cụ thể hơn

---

## Xử Lý Lỗi

### Return Error Là Giá Trị Cuối Cùng

Luôn return error làm parameter cuối cùng trong function multi-return.

✅ **Tốt:**
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

❌ **Tệ:**
```go
func ReadUser(id int) (error, *User) {  // Error nên là cuối cùng
    // implementation
}

func ProcessData() (error, string, int) {  // Error nên là cuối cùng
    // implementation
}

// Bỏ qua error
func ReadUser(id int) *User {
    user, _ := db.GetUser(id)  // Bỏ qua error!
    return user
}
```

**Tại sao nên tuân theo?** Đây là quy ước chuẩn của Go. Nó làm cho error handling nhất quán trong toàn bộ code Go.

### Tránh Panic Trừ Trong Tình Huống Đặc Biệt

Chỉ sử dụng panic cho những điều kiện thực sự đặc biệt đại diện cho lỗi của programmer.

✅ **Tốt:**
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

// Panic chỉ cho lỗi programmer hoặc initialization
func init() {
    if os.Getenv("REQUIRED_CONFIG") == "" {
        panic("REQUIRED_CONFIG environment variable must be set")
    }
}
```

❌ **Tệ:**
```go
func ReadUser(id int) *User {
    user, err := db.GetUser(id)
    if err != nil {
        panic(err)  // Đừng panic cho recoverable error!
    }
    return user
}

func ProcessFile(filename string) {
    file, err := os.Open(filename)
    if err != nil {
        panic("file not found")  // Điều này nên được return làm error
    }
    defer file.Close()
    // xử lý file
}
```

**Tại sao nên tuân theo?** Panic làm crash chương trình. Error có thể được xử lý gracefully bởi caller.

**Ưu điểm:** Buộc phải xử lý error đúng cách, ứng dụng robust hơn
**Nhược điểm:** Dài dòng hơn, cần xử lý error ở mỗi level

### Sử Dụng Wrapped Error với fmt.Errorf và %w

Wrap error để cung cấp context trong khi vẫn bảo tồn error gốc để kiểm tra.

✅ **Tốt:**
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

// Kiểm tra error với errors.Is
func HandleError(err error) {
    if errors.Is(err, sql.ErrNoRows) {
        log.Println("No data found")
        return
    }
    log.Printf("Unexpected error: %v", err)
}
```

❌ **Tệ:**
```go
func SaveUser(user *User) error {
    if err := validateUser(user); err != nil {
        return fmt.Errorf("user validation failed: %s", err.Error())  // Mất error gốc
    }
    
    if err := db.Insert(user); err != nil {
        return errors.New("database error")  // Mất context và error gốc
    }
    
    return nil
}

// Tạo error mới thay vì wrapping
func ProcessData() error {
    _, err := fetchData()
    if err != nil {
        return errors.New("data fetch failed")  // Error gốc bị mất
    }
    return nil
}
```

**Tại sao nên tuân theo?** Error wrapping bảo tồn error chain để debug trong khi thêm context.

**Ưu điểm:** Debug tốt hơn, kiểm tra error với errors.Is/As, bảo tồn context
**Nhược điểm:** Error message dài hơn, cần hiểu về error wrapping

---

## Phong Cách Code Chung

### Luôn Sử Dụng gofmt hoặc goimports

Format code tự động để duy trì tính nhất quán.

✅ **Tốt:**
```go
// Được format đúng với gofmt/goimports
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

❌ **Tệ:**
```go
// Format kém, spacing không nhất quán
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

**Tại sao nên tuân theo?** Format nhất quán làm code dễ đọc và loại bỏ tranh luận về style.

**Ưu điểm:** Nhất quán, không tranh luận về format, tự động
**Nhược điểm:** Phải setup tooling

### Tránh Deep Nesting; Sử Dụng Early Return

Giảm cognitive load bằng cách xử lý error case sớm và tránh indentation sâu.

✅ **Tốt:**
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
            continue  // Early continue thay vì nesting
        }
        
        if user.LastLogin.IsZero() {
            continue
        }
        
        activeUsers = append(activeUsers, user)
    }
    
    return activeUsers
}
```

❌ **Tệ:**
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

**Tại sao nên tuân theo?** Giảm cognitive load và làm cho happy path rõ ràng hơn.

**Ưu điểm:** Dễ đọc hơn, dễ theo dõi logic hơn, ít tốn mental overhead
**Nhược điểm:** Có thể dẫn đến nhiều return statement hơn

### Ưu Tiên Tên Hàm Ngắn, Rõ Ràng và Logic

Viết function làm một việc tốt với tên rõ ràng, súc tích.

✅ **Tốt:**
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

❌ **Tệ:**
```go
func ValidateEmailAddressAndReturnBooleanIndicatingWhetherItIsValidOrNot(email string) bool {
    // Tên quá dài dòng
    return strings.Contains(email, "@") && strings.Contains(email, ".")
}

func ProcessUserDataAndValidateAndSaveToDatabase(userData map[string]interface{}) error {
    // Function làm quá nhiều việc
    
    // Logic validation
    email, ok := userData["email"].(string)
    if !ok || !strings.Contains(email, "@") {
        return errors.New("invalid email")
    }
    
    name, ok := userData["name"].(string)
    if !ok || len(name) < 2 {
        return errors.New("invalid name")
    }
    
    // Logic hash password
    password, ok := userData["password"].(string)
    if !ok || len(password) < 8 {
        return errors.New("invalid password")
    }
    
    hash, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
    if err != nil {
        return err
    }
    
    // Logic save database
    query := "INSERT INTO users (name, email, password_hash) VALUES (?, ?, ?)"
    _, err = db.Exec(query, name, email, string(hash))
    return err
}
```

**Tại sao nên tuân theo?** Single responsibility làm cho function dễ test, hiểu và tái sử dụng.

**Ưu điểm:** Dễ test hơn, tái sử dụng tốt hơn, ý định rõ ràng hơn
**Nhược điểm:** Nhiều function hơn để quản lý, cần tìm ranh giới tốt

---

## Checklist Tham Khảo Nhanh

Trước khi submit code, hãy tự hỏi:

- [ ] Tên biến/function của tôi có đúng camelCase (nếu unexported) hoặc PascalCase (nếu exported) không?
- [ ] Hằng số của tôi có được format đúng (SCREAMING_SNAKE_CASE hoặc CamelCase) không?
- [ ] Tôi có return error làm parameter cuối cùng không?
- [ ] Tôi có đang wrap error với context sử dụng `fmt.Errorf` và `%w` không?
- [ ] Tôi có tránh deep nesting với early return không?
- [ ] Function của tôi có tập trung vào single responsibility không?
- [ ] Tôi có chạy `gofmt` hoặc `goimports` trên code không?
- [ ] Tôi có sử dụng đúng receiver type (pointer vs value) một cách nhất quán không?

## Tài Liệu Tham Khảo Thêm

- [Effective Go](https://golang.org/doc/effective_go.html) - Tài liệu chính thức của Go
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments) - Feedback review phổ biến
- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md) - Style guide toàn diện

Nhớ rằng: Những quy ước này tồn tại để làm cho code của chúng ta nhất quán, dễ đọc và dễ maintain. Khi nghi ngờ, hãy ưu tiên sự rõ ràng và đơn giản!
