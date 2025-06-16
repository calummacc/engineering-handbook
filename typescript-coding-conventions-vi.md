# Hướng Dẫn Quy Ước Code TypeScript

Chào mừng bạn đến với quy ước code TypeScript của team! Tài liệu này sẽ giúp bạn viết code sạch, dễ bảo trì và an toàn về kiểu dữ liệu theo tiêu chuẩn của team.

## 1. Quy Ước Đặt Tên

### Biến và Hàm: camelCase

**Quy tắc:** Sử dụng camelCase cho biến, hàm và phương thức.

```typescript
// ✅ Tốt
const userName = 'john_doe';
const totalPrice = 199.99;

function calculateTax(amount: number): number {
  return amount * 0.08;
}

// ❌ Không tốt
const user_name = 'john_doe';      // snake_case
const TotalPrice = 199.99;         // PascalCase
const TOTAL_PRICE = 199.99;        // SCREAMING_SNAKE_CASE (trừ khi là hằng số)

function CalculateTax(amount: number): number {  // PascalCase
  return amount * 0.08;
}
```

**✅ Ưu điểm:**
- Thống nhất với quy ước JavaScript
- Dễ đọc và được sử dụng rộng rãi
- Được hỗ trợ bởi hầu hết các linter và formatter

**❌ Nhược điểm:**
- Không có nhược điểm đáng kể - đây là tiêu chuẩn được thiết lập

**Tại sao nên tuân theo:** camelCase là tiêu chuẩn JavaScript/TypeScript. Tính nhất quán trong codebase giúp các thành viên team đọc và bảo trì code dễ dàng hơn.

### Class và Interface: PascalCase

**Quy tắc:** Sử dụng PascalCase cho class, interface, type và enum.

```typescript
// ✅ Tốt
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

// ❌ Không tốt
class userService { }           // camelCase
interface apiResponse { }       // camelCase
type databaseConnection = { };  // camelCase
enum orderStatus { }           // camelCase
```

**✅ Ưu điểm:**
- Phân biệt rõ ràng giữa type/class và biến/hàm
- Tuân theo quy ước TypeScript và các ngôn ngữ khác
- Làm code dễ đọc hơn

**❌ Nhược điểm:**
- Không có - đây là tiêu chuẩn được thiết lập

**Tại sao nên tuân theo:** PascalCase cho type và class là quy ước phổ biến giúp phân biệt các loại định danh khác nhau một cách dễ dàng.

### Hằng Số: SCREAMING_SNAKE_CASE

**Quy tắc:** Sử dụng SCREAMING_SNAKE_CASE cho hằng số cấp module và giá trị cấu hình.

```typescript
// ✅ Tốt
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_TIMEOUT_MS = 5000;

// ✅ Cũng chấp nhận được cho hằng số local không thay đổi
const TAX_RATE = 0.08;

// ❌ Không tốt
const apiBaseUrl = 'https://api.example.com';     // camelCase cho hằng số
const maxRetryAttempts = 3;                       // camelCase cho hằng số
```

**✅ Ưu điểm:**
- Ngay lập tức nhận biết được giá trị không thay đổi
- Ngăn chặn việc sửa đổi không cố ý
- Tiêu chuẩn chung của nhiều ngôn ngữ lập trình

**❌ Nhược điểm:**
- Có thể dài dòng
- Không phải lúc nào cũng cần thiết cho hằng số local đơn giản

**Tại sao nên tuân theo:** Làm rõ những giá trị nào được thiết kế là cấu hình cố định, giúp ngăn chặn lỗi và cải thiện khả năng đọc code.

### Đặt Tên Interface: Không Dùng Tiền Tố "I"

**Quy tắc:** Không thêm tiền tố "I" vào interface. Sử dụng tên mô tả thay thế.

```typescript
// ✅ Tốt
interface User {
  id: string;
  name: string;
  email: string;
}

interface PaymentProcessor {
  processPayment(amount: number): Promise<boolean>;
}

// ❌ Không tốt
interface IUser {           // Tiền tố "I" không cần thiết
  id: string;
  name: string;
  email: string;
}

interface IPaymentProcessor {  // Tiền tố "I" không cần thiết
  processPayment(amount: number): Promise<boolean>;
}
```

**✅ Ưu điểm:**
- Phong cách TypeScript hiện đại và sạch sẽ hơn
- Tập trung vào ý nghĩa của interface, không phải bản chất kỹ thuật
- Được khuyến nghị bởi team TypeScript

**❌ Nhược điểm:**
- Một số developer từ nền tảng C# có thể ưa thích tiền tố "I"
- Ít rõ ràng hơn một chút về việc đây là interface

**Tại sao nên tuân theo:** Quy ước TypeScript hiện đại. Tiền tố "I" được coi là lỗi thời và tạo ra tiếng ồn không cần thiết trong tên interface.

## 2. Hướng Dẫn Type vs Interface

### Sử Dụng Interface Cho Hình Dạng Object

**Quy tắc:** Ưu tiên `interface` khi định nghĩa hình dạng của object, đặc biệt khi có thể cần mở rộng sau này.

```typescript
// ✅ Tốt - Sử dụng interface cho hình dạng object
interface User {
  id: string;
  name: string;
  email: string;
}

interface AdminUser extends User {
  permissions: string[];
}

// ✅ Cũng tốt - Interface có thể được bổ sung
interface ApiClient {
  get(url: string): Promise<Response>;
}

// Sau này trong file khác
interface ApiClient {
  post(url: string, data: unknown): Promise<Response>;
}

// ❌ Kém lý tưởng hơn - Type cho hình dạng object đơn giản
type User = {
  id: string;
  name: string;
  email: string;
};
```

**✅ Ưu điểm:**
- Có thể được mở rộng và gộp lại
- Linh hoạt hơn cho những thay đổi trong tương lai
- Tốt hơn để định nghĩa contract

**❌ Nhược điểm:**
- Hơi dài dòng hơn type alias
- Không thể biểu diễn union hoặc computed type

**Tại sao nên tuân theo:** Interface có khả năng mở rộng tốt hơn và hoạt động tốt với declaration merging của TypeScript, làm chúng trở nên lý tưởng cho hình dạng object có thể phát triển.

### Sử Dụng Type Cho Kiểu Phức Tạp

**Quy tắc:** Sử dụng `type` cho union, intersection, computed type và các thao tác type phức tạp.

```typescript
// ✅ Tốt - Sử dụng type cho union
type Theme = 'light' | 'dark' | 'auto';
type Status = 'loading' | 'success' | 'error';

// ✅ Tốt - Sử dụng type cho intersection
type UserWithProfile = User & Profile;

// ✅ Tốt - Sử dụng type cho computed/mapped type
type PartialUser = Partial<User>;
type UserKeys = keyof User;

// ✅ Tốt - Sử dụng type cho function signature
type EventHandler = (event: Event) => void;
type AsyncFunction<T> = () => Promise<T>;

// ❌ Không tốt - Interface không thể biểu diễn union
interface Theme {  // Không hoạt động cho 'light' | 'dark' | 'auto'
  // ...
}
```

**✅ Ưu điểm:**
- Có thể biểu diễn các thao tác type phức tạp
- Ngắn gọn hơn cho type alias đơn giản
- Bắt buộc cho union và intersection

**❌ Nhược điểm:**
- Không thể được mở rộng bằng từ khóa `extends`
- Không thể được bổ sung/gộp lại

**Tại sao nên tuân theo:** Type mạnh mẽ hơn cho các thao tác type phức tạp, trong khi interface xuất sắc trong việc định nghĩa contract object.

## 3. Best Practice Về Type Safety

### Tránh `any` - Sử Dụng Type Cụ Thể

**Quy tắc:** Không bao giờ sử dụng `any` trừ khi thực sự cần thiết. Luôn cố gắng định nghĩa type cụ thể.

```typescript
// ✅ Tốt - Type cụ thể
interface ApiResponse {
  data: User[];
  status: number;
  message: string;
}

function processResponse(response: ApiResponse): User[] {
  return response.data;
}

// ✅ Tốt - Sử dụng generic để linh hoạt
function processApiResponse<T>(response: { data: T; status: number }): T {
  return response.data;
}

// ❌ Không tốt - Sử dụng any mất hết type safety
function processResponse(response: any): any {
  return response.data;  // Không có type checking, không có IntelliSense
}
```

**✅ Ưu điểm:**
- Hỗ trợ đầy đủ type checking và IntelliSense
- Phát hiện lỗi tại thời điểm compile
- Code tự documenting

**❌ Nhược điểm:**
- Cần nỗ lực ban đầu để định nghĩa type
- Có thể cần học các tính năng TypeScript nâng cao

**Tại sao nên tuân theo:** `any` làm mất đi mục đích của việc sử dụng TypeScript. Type cụ thể giúp phát hiện lỗi sớm và làm code dễ bảo trì hơn.

### Sử Dụng `unknown` Cho Type Không Chắc Chắn

**Quy tắc:** Khi thực sự không biết type của dữ liệu, sử dụng `unknown` thay vì `any`.

```typescript
// ✅ Tốt - Sử dụng unknown bắt buộc type checking
function parseJsonSafely(jsonString: string): unknown {
  try {
    return JSON.parse(jsonString);
  } catch {
    return null;
  }
}

function processUnknownData(data: unknown): string {
  // Phải kiểm tra type trước khi sử dụng
  if (typeof data === 'string') {
    return data.toUpperCase();
  }
  if (typeof data === 'object' && data !== null && 'message' in data) {
    return String((data as { message: unknown }).message);
  }
  return 'Dữ liệu không hợp lệ';
}

// ❌ Không tốt - any cho phép thao tác không an toàn
function parseJsonUnsafely(jsonString: string): any {
  return JSON.parse(jsonString);
}

function processAnyData(data: any): string {
  return data.toUpperCase();  // Lỗi runtime nếu data không phải string!
}
```

**✅ Ưu điểm:**
- Duy trì type safety
- Bắt buộc kiểm tra type rõ ràng
- Ngăn chặn lỗi runtime

**❌ Nhược điểm:**
- Yêu cầu type guard và kiểm tra bổ sung
- Dài dòng hơn `any`

**Tại sao nên tuân theo:** `unknown` là `any` an toàn về type. Nó bắt buộc bạn kiểm tra type trước khi sử dụng giá trị, ngăn chặn lỗi runtime.

### Luôn Chỉ Định Return Type Cho Hàm

**Quy tắc:** Khai báo rõ ràng return type cho tất cả hàm, đặc biệt là public API.

```typescript
// ✅ Tốt - Return type rõ ràng
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

// ❌ Không tốt - Return type ngầm định
function calculateTotal(items: Item[]) {  // Return type được suy ra là number
  return items.reduce((sum, item) => sum + item.price, 0);
}

async function fetchUser(id: string) {    // Return type được suy ra là Promise<any>
  const response = await fetch(`/api/users/${id}`);
  return await response.json();
}
```

**✅ Ưu điểm:**
- Làm contract của hàm trở nên rõ ràng
- Phát hiện lỗi return type sớm
- Cải thiện documentation của code
- IntelliSense tốt hơn cho người gọi hàm

**❌ Nhược điểm:**
- Hơi dài dòng
- Cần cập nhật nếu return type thay đổi

**Tại sao nên tuân theo:** Return type rõ ràng đóng vai trò như documentation và phát hiện lỗi khi hàm có thể trả về type không mong muốn.

## 4. Best Practice Về Code Style Chung

### Sử Dụng `const` Thay Vì `let`

**Quy tắc:** Sử dụng `const` theo mặc định, chỉ dùng `let` khi cần gán lại giá trị cho biến.

```typescript
// ✅ Tốt - Sử dụng const cho giá trị không thay đổi
const users = [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }];
const apiUrl = 'https://api.example.com';

function processUsers(): void {
  const filteredUsers = users.filter(user => user.id > 0);
  const userCount = filteredUsers.length;
  
  // ✅ Tốt - let chỉ khi cần gán lại
  let message = 'Đang xử lý users...';
  if (userCount === 0) {
    message = 'Không tìm thấy user';
  }
  
  console.log(message);
}

// ❌ Không tốt - Sử dụng let không cần thiết
let users = [{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }];  // Không bao giờ gán lại
let apiUrl = 'https://api.example.com';                           // Không bao giờ gán lại

function processUsers(): void {
  let filteredUsers = users.filter(user => user.id > 0);         // Không bao giờ gán lại
  let userCount = filteredUsers.length;                          // Không bao giờ gán lại
}
```

**✅ Ưu điểm:**
- Ngăn chặn việc gán lại không cố ý
- Làm ý định code rõ ràng hơn
- Giúp phát hiện lỗi sớm
- Chỉ ra immutable binding

**❌ Nhược điểm:**
- Cần suy nghĩ xem có cần gán lại không
- Phải đổi thành `let` nếu yêu cầu thay đổi

**Tại sao nên tuân theo:** `const` làm code dự đoán được hơn và ngăn chặn một nguồn lỗi phổ biến (gán lại không cố ý).

### Tránh Nesting Sâu

**Quy tắc:** Giữ mức độ nesting tối thiểu. Sử dụng early return, guard clause và extracted function.

```typescript
// ✅ Tốt - Early return và guard clause
function processUser(user: User | null): string {
  if (!user) {
    return 'Không tìm thấy user';
  }
  
  if (!user.email) {
    return 'Thiếu email của user';
  }
  
  if (!isValidEmail(user.email)) {
    return 'Định dạng email không hợp lệ';
  }
  
  return `Đang xử lý user: ${user.name}`;
}

// ✅ Tốt - Tách logic phức tạp thành hàm riêng
function validateOrder(order: Order): ValidationResult {
  const userValidation = validateUser(order.user);
  if (!userValidation.isValid) {
    return userValidation;
  }
  
  const itemsValidation = validateItems(order.items);
  if (!itemsValidation.isValid) {
    return itemsValidation;
  }
  
  return { isValid: true, message: 'Order hợp lệ' };
}

// ❌ Không tốt - Nesting sâu
function processUser(user: User | null): string {
  if (user) {
    if (user.email) {
      if (isValidEmail(user.email)) {
        if (user.isActive) {
          if (user.permissions.length > 0) {
            return `Đang xử lý user active: ${user.name}`;
          } else {
            return 'User không có quyền';
          }
        } else {
          return 'User không active';
        }
      } else {
        return 'Định dạng email không hợp lệ';
      }
    } else {
      return 'Thiếu email của user';
    }
  } else {
    return 'Không tìm thấy user';
  }
}
```

**✅ Ưu điểm:**
- Dễ đọc và hiểu hơn
- Giảm tải nhận thức
- Dễ test từng điều kiện riêng lẻ
- Ít prone to logic error hơn

**❌ Nhược điểm:**
- Có thể tạo ra nhiều dòng code hơn
- Nhiều return point (một số người coi đây là nhược điểm)

**Tại sao nên tuân theo:** Nesting sâu làm code khó đọc và khó reasoning. Cấu trúc code phẳng dễ hiểu và bảo trì hơn.

### Ưu Tiên Destructuring và Spread Syntax

**Quy tắc:** Sử dụng destructuring để trích xuất giá trị và spread syntax để copy/merge object và array.

```typescript
// ✅ Tốt - Object destructuring
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
  console.log(`Đang xử lý user ${id}: ${name}`);
  updateUserSettings(settings);
}

// ✅ Tốt - Array destructuring
const coordinates = [10, 20, 30];
const [x, y, z] = coordinates;

// ✅ Tốt - Spread syntax để copy/merge
const originalUser: User = { id: '1', name: 'Alice', email: 'alice@example.com', settings: {} };
const updatedUser: User = {
  ...originalUser,
  name: 'Alice Smith',
  settings: { ...originalUser.settings, theme: 'dark' }
};

const numbers = [1, 2, 3];
const moreNumbers = [...numbers, 4, 5, 6];

// ❌ Không tốt - Truy cập property thủ công
function displayUser(user: User): string {
  return `${user.name} (${user.email})`;  // Có thể dùng destructuring
}

function processUser(user: User): void {
  const id = user.id;          // Dài dòng so với destructuring
  const name = user.name;
  const settings = user.settings;
  console.log(`Đang xử lý user ${id}: ${name}`);
}

// ❌ Không tốt - Copy thủ công
const updatedUser: User = {
  id: originalUser.id,
  name: 'Alice Smith',         // Copy thủ công từng property
  email: originalUser.email,
  settings: originalUser.settings
};
```

**✅ Ưu điểm:**
- Ngắn gọn và dễ đọc hơn
- Giảm code lặp lại
- Làm data flow rõ ràng hơn
- Giúp ngăn chặn mutation bug

**❌ Nhược điểm:**
- Có thể gây confuse cho người mới bắt đầu
- Có thể làm debug khó hơn trong một số trường hợp
- Không phải lúc nào cũng dễ đọc hơn cho case đơn giản

**Tại sao nên tuân theo:** Destructuring và spread syntax là tính năng JavaScript/TypeScript hiện đại giúp code ngắn gọn hơn và ngăn chặn các lỗi phổ biến liên quan đến object mutation.

## Mẹo Bổ Sung Để Thành Công

### 1. Bật Strict Mode
Luôn sử dụng strict mode của TypeScript trong `tsconfig.json`:

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

### 2. Sử Dụng Tên Có Ý Nghĩa
Chọn tên mô tả giải thích code làm gì:

```typescript
// ✅ Tốt
const activeUsersCount = users.filter(user => user.isActive).length;
const isEmailValid = validateEmail(email);

// ❌ Không tốt
const count = users.filter(u => u.active).length;
const valid = check(email);
```

### 3. Viết Code Tự Giải Thích
Cố gắng viết code tự giải thích bản thân:

```typescript
// ✅ Tốt - Ý định rõ ràng
function canUserAccessResource(user: User, resource: Resource): boolean {
  return user.permissions.includes(resource.requiredPermission) && 
         user.isActive && 
         !resource.isRestricted;
}

// ❌ Không tốt - Logic không rõ ràng
function check(u: User, r: Resource): boolean {
  return u.perms.includes(r.perm) && u.active && !r.restricted;
}
```

Hãy nhớ: Những quy ước này tồn tại để làm code của chúng ta dễ đọc, dễ bảo trì và ít lỗi hơn. Khi nghi ngờ, hãy ưu tiên tính rõ ràng và nhất quán hơn là tính ngắn gọn. Chúc bạn code vui vẻ!
