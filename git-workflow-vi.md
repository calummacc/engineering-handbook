# Quy Trình Git & Quy Ước Đặt Tên Nhánh Cho Nhóm

## Tổng Quan

Hướng dẫn này thiết lập các thực hành Git nhất quán cho nhóm của chúng ta. Tuân theo các quy ước này đảm bảo lịch sử code sạch sẽ, cộng tác dễ dàng hơn và triển khai mượt mà hơn. Dù bạn là thực tập sinh hay lập trình viên có kinh nghiệm, những thực hành này sẽ giúp duy trì chất lượng code và năng suất nhóm.

## Nguyên Tắc Sử Dụng Git Chung

### Quy Tắc Cốt Lõi

**Commit Sớm và Thường Xuyên**
- Tạo các commit nhỏ, tập trung vào một thay đổi logic duy nhất
- Không chờ đến cuối ngày mới commit công việc của bạn
- Tốt hơn là có nhiều commit nhỏ thay vì một commit lớn

**Không Bao Giờ Commit Trực Tiếp Vào Nhánh Được Bảo Vệ**
- Không bao giờ push commit trực tiếp vào `main`, `staging`, hoặc `production`
- Luôn làm việc trên nhánh feature và sử dụng pull request
- Điều này đảm bảo code review và duy trì tính ổn định của nhánh

**Luôn Sử Dụng Pull Request (PR)**
- Tất cả thay đổi code phải qua pull request review
- PR cung cấp cơ hội cho code review, thảo luận và kiểm thử tự động
- Chỉ merge sau khi được ít nhất một thành viên trong nhóm phê duyệt

## Quy Ước Đặt Tên Nhánh

### Với Tích Hợp Jira

Khi sử dụng Jira để quản lý dự án, bao gồm ID ticket trong tên nhánh:

```
feature/JIRA-123-implement-user-authentication
bugfix/JIRA-456-fix-password-validation
hotfix/JIRA-789-fix-production-login-error
```

### Không Sử Dụng Jira

Đối với các nhóm không sử dụng Jira, sử dụng tên mô tả rõ ràng mục đích:

```
feature/user-authentication
feature/payment-integration
bugfix/fix-signup-validation
bugfix/resolve-memory-leak
hotfix/fix-production-crash
```

### Tiền Tố Loại Nhánh

- `feature/` - Tính năng mới hoặc cải tiến
- `bugfix/` - Sửa lỗi cho các vấn đề không phải production
- `hotfix/` - Sửa lỗi quan trọng cho production
- `chore/` - Công việc bảo trì, cập nhật dependency
- `docs/` - Thay đổi chỉ liên quan đến tài liệu

### Các Nhánh Chính

Quy trình của chúng ta sử dụng ba nhánh chính sống lâu:

- `production` - Code sẵn sàng cho production, luôn ổn định
- `staging` - Môi trường kiểm thử trước production
- `dev` - Nhánh tích hợp phát triển

## Quy Ước Commit Message

### Với Tích Hợp Jira

Bắt đầu commit message với ID ticket Jira:

```
JIRA-123: Implement user authentication with OAuth
JIRA-456: Fix password validation regex
JIRA-789: Add error handling for API timeouts
```

### Không Sử Dụng Jira (Conventional Commits)

Sử dụng định dạng: `type(scope): mô tả`

```
feat(auth): add OAuth integration for user login
fix(validation): correct password regex pattern
chore(deps): update React to version 18.2
refactor(api): simplify user service methods
docs(readme): add installation instructions
test(auth): add unit tests for login flow
build(docker): optimize container image size
```

### Các Loại Commit

- `feat` - Tính năng mới
- `fix` - Sửa lỗi
- `chore` - Công việc bảo trì
- `refactor` - Tái cấu trúc code không thay đổi tính năng
- `docs` - Thay đổi tài liệu
- `test` - Thêm hoặc cập nhật test
- `build` - Thay đổi hệ thống build hoặc dependency
- `perf` - Cải thiện hiệu suất
- `style` - Thay đổi style code (định dạng, v.v.)

## Quy Tắc Merge Nhánh

### Phân Cấp Luồng Merge

Chiến lược branching của chúng ta tuân theo phân cấp nghiêm ngặt để đảm bảo tính ổn định của code:

```
feature/bugfix/hotfix branches → dev → staging → production
```

### Quy Tắc Cụ Thể

1. **Nhánh Production**
   - Chỉ chấp nhận PR từ `staging`
   - Đại diện cho code hiện đang chạy trong production
   - Phải luôn ổn định và có thể triển khai

2. **Nhánh Staging**
   - Chỉ chấp nhận PR từ `dev`
   - Sử dụng cho kiểm thử trước production và QA
   - Nên phản ánh môi trường production

3. **Nhánh Dev**
   - Chấp nhận PR từ `feature/*`, `bugfix/*`, `hotfix/*`
   - Nhánh tích hợp cho phát triển đang diễn ra
   - Có thể kém ổn định hơn staging/production

4. **Nhánh Feature/Bugfix/Hotfix**
   - Tạo từ `dev` (hoặc `production` cho hotfix quan trọng)
   - Merge trở lại `dev` qua pull request
   - Xóa sau khi merge thành công

### Quy Tắc Quan Trọng
**Không bao giờ merge nhánh feature trực tiếp vào `staging` hoặc `production`**. Tất cả thay đổi phải đi qua phân cấp thích hợp.

## Ví Dụ

### Tên Nhánh Tốt vs Xấu

**✅ Tên Nhánh Tốt:**
```
feature/JIRA-234-user-profile-page
bugfix/JIRA-567-fix-cart-calculation
hotfix/JIRA-890-fix-payment-gateway
feature/shopping-cart-ui
bugfix/fix-mobile-layout
```

**❌ Tên Nhánh Xấu:**
```
my-feature
john-branch
test
fix
branch1
temp-branch
```

### Commit Message Tốt vs Xấu

**✅ Commit Message Tốt:**
```
JIRA-123: Add user authentication with JWT tokens
feat(cart): implement add to cart functionality
fix(validation): resolve email format validation issue
chore(deps): update lodash to version 4.17.21
docs(api): add endpoint documentation for user service
```

**❌ Commit Message Xấu:**
```
fixed stuff
update
changes
wip
asdf
minor tweaks
```

### Kịch Bản Merge Đúng vs Sai

**✅ Luồng Merge Đúng:**
```
1. Tạo feature/JIRA-123-login-page từ dev
2. Làm việc trên feature, tạo các commit
3. Tạo PR: feature/JIRA-123-login-page → dev
4. Sau khi phê duyệt và merge, tạo PR: dev → staging
5. Sau khi test staging, tạo PR: staging → production
```

**❌ Luồng Merge Sai:**
```
1. Tạo feature/login-page từ dev
2. Làm việc trên feature, tạo các commit
3. Tạo PR: feature/login-page → production (SAI!)
4. Hoặc: Tạo PR: feature/login-page → staging (CŨNG SAI!)
```

**✅ Ngoại Lệ Hotfix:**
```
1. Tạo hotfix/JIRA-999-critical-fix từ production
2. Sửa vấn đề quan trọng, commit thay đổi
3. Tạo PR: hotfix/JIRA-999-critical-fix → production
4. Cũng merge nhánh hotfix trở lại dev và staging để giữ đồng bộ
```

## Tóm Tắt Quy Trình Hàng Ngày

1. **Bắt Đầu Công Việc Mới:**
   - Pull thay đổi mới nhất từ `dev`
   - Tạo nhánh feature: `git checkout -b feature/JIRA-123-feature-name`

2. **Trong Quá Trình Phát Triển:**
   - Tạo các commit nhỏ, tập trung với message rõ ràng
   - Push nhánh thường xuyên: `git push origin feature/JIRA-123-feature-name`

3. **Sẵn Sàng Cho Review:**
   - Tạo pull request từ nhánh feature của bạn tới `dev`
   - Yêu cầu review từ các thành viên trong nhóm
   - Giải quyết feedback và thực hiện các thay đổi cần thiết

4. **Sau Khi Merge:**
   - Xóa nhánh feature ở local và remote
   - Pull thay đổi mới nhất từ `dev` trước khi bắt đầu feature tiếp theo

## Thực Hành Tốt Nhất

- **Rebase trước khi tạo PR** để giữ lịch sử commit sạch sẽ
- **Test các thay đổi** trước khi tạo pull request
- **Giữ PR nhỏ** và tập trung vào một tính năng/sửa lỗi duy nhất
- **Viết mô tả PR chi tiết** giải thích cái gì và tại sao
- **Phản hồi nhanh chóng** với feedback code review
- **Không force push** vào nhánh được chia sẻ
- **Sử dụng draft PR** cho công việc đang tiến hành cần feedback sớm

Tuân theo các quy ước này sẽ giúp duy trì codebase sạch sẽ, có tổ chức và cộng tác mượt mà trong nhóm. Khi có nghi ngờ, hãy hỏi thành viên senior trong nhóm để được hướng dẫn!
