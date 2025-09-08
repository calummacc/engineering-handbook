# 📘 Engineering Handbook – Database Naming Conventions (v2.0 Full)

## 1. Mục tiêu

Thống nhất cách đặt tên trong database, giúp:

* Code dễ đọc, dễ review, dễ query.
* Tránh nhầm lẫn giữa các dev, module, service.
* Thuận tiện khi scale hệ thống, audit dữ liệu.

---

## 2. Quy tắc chung

* **Ngôn ngữ:** Tiếng Anh.
* **Style:** `snake_case`.
* **Table:** số nhiều.
* **Field:** số ít.
* **Boolean:** dùng prefix `is_`, `has_`, `can_`.
* **Datetime:** luôn kết thúc bằng `_at`.
* **Date (không có time):** luôn kết thúc bằng `_on`.
* **Không dùng từ khóa SQL** (select, order, group…).

---

## 3. Quy tắc đặt tên Table

1. **Số nhiều:** `users`, `orders`.
2. **Phân loại theo tiền tố:**

   * `ref_` → reference table (lookup): `ref_countries`.
   * `map_` → mapping table (n-n relation): `map_user_roles`.
   * `log_` → logging/audit: `log_api_requests`.
   * `tmp_` → bảng tạm: `tmp_daily_report`.
3. **Audit fields mặc định:**

   * `created_at`, `updated_at`, `deleted_at`.
   * `created_by`, `updated_by` (nếu cần).

---

## 4. Quy tắc đặt tên Field

### 4.1 Tiền tố (prefix)

| Prefix          | Ý nghĩa            | Ví dụ                     |
| --------------- | ------------------ | ------------------------- |
| `is_`           | Trạng thái boolean | `is_active`, `is_deleted` |
| `has_`          | Tồn tại/sở hữu     | `has_avatar`, `has_paid`  |
| `can_`          | Quyền hạn          | `can_edit`, `can_delete`  |
| `min_`/`max_`   | Giới hạn           | `min_price`, `max_users`  |
| `start_`/`end_` | Khoảng thời gian   | `start_date`, `end_time`  |
| `src_`/`dst_`   | Nguồn/đích         | `src_ip`, `dst_ip`        |
| `last_`         | Lần cuối            | `lastUsedAt`, `lastLoginAt` |
| `oauth_`        | OAuth related       | `oauthProvider`, `oauthId` |

### 4.2 Hậu tố (suffix)

| Suffix      | Ý nghĩa                | Ví dụ                         |
| ----------- | ---------------------- | ----------------------------- |
| `_id`       | Khóa ngoại / định danh | `user_id`, `order_id`         |
| `_at`       | Datetime               | `created_at`, `deleted_at`    |
| `_on`       | Date                   | `expired_on`, `published_on`  |
| `_by`       | User tác động          | `created_by`, `updated_by`    |
| `_count`    | Số lượng               | `view_count`, `comment_count` |
| `_url`      | Link                   | `avatar_url`, `website_url`   |
| `_ip`       | Địa chỉ IP             | `login_ip`, `request_ip`      |
| `_code`     | Mã code                | `country_code`, `status_code` |
| `_key`      | Secret/token           | `api_key`, `secret_key`       |
| `_type`     | Loại                   | `user_type`, `payment_type`   |
| `_status`   | Trạng thái             | `file_status`, `order_status` |
| `_name`     | Tên                    | `user_name`, `file_name`      |
| `_path`     | Đường dẫn              | `file_path`, `image_path`     |
| `_size`     | Kích thước             | `file_size`, `image_size`     |
| `_length`   | Độ dài                 | `name_length`, `desc_length`  |
| `_version`  | Phiên bản              | `schema_version`, `api_version` |
| `_metadata` | Dữ liệu bổ sung        | `file_metadata`, `user_metadata` |
| `_extra`    | Thông tin thêm         | `plan_extra`, `config_extra`  |

---

## 5. Quy tắc đặt tên Index

* Format: `idx_<table>_<field1>_<field2>`
* Ví dụ:

  * `idx_users_email`
  * `idx_orders_user_id_created_at`
* Index unique: thêm `_uniq`.

  * `idx_users_email_uniq`
* Index composite: sắp xếp theo độ ưu tiên query.

  * `idx_users_status_role` (status trước vì filter nhiều hơn)
  * `idx_qr_tickets_status_expires_at`
* Index cho foreign key: `idx_<table>_<fk_field>`.

  * `idx_api_keys_plan_id`
  * `idx_rate_limit_logs_api_key`

---

## 6. Quy tắc đặt tên Constraint

* **Primary key:** `pk_<table>` → `pk_users`
* **Foreign key:** `fk_<child_table>_<parent_table>` → `fk_orders_users`
* **Foreign key với column cụ thể:** `fk_<child_table>_<parent_table>_<column>` → `fk_api_keys_plans_plan_id`
* **Unique constraint:** `uq_<table>_<field>` → `uq_users_email`
* **Unique constraint composite:** `uq_<table>_<field1>_<field2>` → `uq_users_oauth_provider_oauth_id`
* **Check constraint:** `ck_<table>_<rule>` → `ck_orders_amount_positive`
* **Check constraint cho enum:** `ck_<table>_<field>_valid` → `ck_users_status_valid`

---

## 7. Quy tắc đặt tên Sequence

* Format: `seq_<table>_<field>`
* Ví dụ: `seq_orders_order_id`

---

## 8. Quy tắc đặt tên View & Materialized View

* View: `vw_<business_name>` → `vw_active_users`
* Materialized view: `mvw_<business_name>` → `mvw_monthly_revenue`

---

## 9. Quy tắc đặt tên Trigger

* Format: `trg_<table>_<action>`
* Ví dụ:

  * `trg_users_before_insert`
  * `trg_orders_after_update`

---

## 10. Quy tắc đặt tên Function / Stored Procedure

* Format: `<action>_<object>`
* Ví dụ:

  * `get_user_by_email`
  * `calculate_order_total`

---

## 11. Quy tắc đặt tên Enum (PostgreSQL)

* Enum type: `enum_<table>_<field>`
* Enum value: viết thường, `snake_case`.
* Ví dụ:

  ```sql
  CREATE TYPE enum_orders_status AS ENUM ('pending', 'completed', 'canceled');
  ```

---

## 12. Quy tắc đặt tên cho ID và UUID

### 12.1 Primary Key Patterns

* **Snowflake ID:** Sử dụng `id` (bigint) cho primary key
* **UUID:** Sử dụng `uuid` cho external references và API responses
* **Composite Key:** Tránh, ưu tiên single primary key

### 12.2 Foreign Key Patterns

* **Format:** `<parent_table>_id` hoặc `<parent_table>Id`
* **Ví dụ:**
  * `user_id` → references `users.id`
  * `plan_id` → references `plans.id`
  * `created_by_id` → references `users.id`

---

## 13. Quy tắc đặt tên cho JSON/JSONB Fields

* **Metadata fields:** `metadata` (JSONB cho structured data)
* **Extra/Config fields:** `extra` (JSONB cho configuration)
* **Payload fields:** `payload` (JSONB cho dynamic data)
* **Settings fields:** `settings` (JSONB cho user preferences)

**Ví dụ:**
```sql
-- File metadata
metadata JSONB -- { "width": 1920, "height": 1080, "format": "jpg" }

-- Plan configuration
extra JSONB -- { "burst": 100, "weight": 1.5, "whitelist": true }

-- QR ticket payload
payload JSONB -- { "action": "login", "redirect": "/dashboard" }
```

---

## 14. Quy tắc đặt tên cho Soft Delete và Versioning

### 14.1 Soft Delete Fields

* **Deleted timestamp:** `deleted_at` (timestamp, nullable)
* **Deleted by:** `deleted_by` (user_id, nullable)
* **Delete reason:** `delete_reason` (varchar, nullable)

### 14.2 Versioning Fields

* **Version number:** `version` (integer, default 1)
* **Schema version:** `schema_version` (varchar, cho migration tracking)
* **API version:** `api_version` (varchar, cho API compatibility)

---

## 15. Quy tắc đặt tên cho Network và Security Fields

### 15.1 IP Address Fields

* **Client IP:** `ip` (inet type cho PostgreSQL)
* **Source IP:** `src_ip` (varchar(45) cho IPv4/IPv6)
* **Destination IP:** `dst_ip` (varchar(45))
* **Whitelist IP:** `whitelist_ip` (inet type)

### 15.2 Security Fields

* **API Key:** `api_key` (varchar, hashed in production)
* **Secret Key:** `secret_key` (varchar, encrypted)
* **Token:** `token` (varchar, for temporary access)
* **Session ID:** `session_id` (varchar, for web sessions)

---

## 16. Quy tắc đặt tên cho Audit và Logging Fields

### 16.1 Audit Fields (mặc định trong BaseEntity)

* **Created:** `created_at`, `created_by`
* **Updated:** `updated_at`, `updated_by`
* **Deleted:** `deleted_at`, `deleted_by`

### 16.2 Logging Fields

* **Request tracking:** `request_id` (varchar, unique per request)
* **User agent:** `user_agent` (text)
* **Referer:** `referer` (varchar)
* **Route key:** `route_key` (varchar, method:path format)

---

## 17. Quy tắc đặt tên cho Enum và Constants

### 17.1 Enum Type Naming

* **Format:** `enum_<table>_<field>`
* **Ví dụ:**
  * `enum_users_status` → ('active', 'inactive', 'suspended')
  * `enum_orders_status` → ('pending', 'completed', 'canceled')
  * `enum_files_type` → ('image', 'document', 'video')

### 17.2 Enum Value Naming

* **Format:** lowercase, snake_case
* **Ví dụ:**
  * `active`, `inactive`, `suspended`
  * `pending`, `completed`, `canceled`
  * `fixed_window`, `sliding_window`, `token_bucket`

---

## 18. Quy tắc đặt tên cho Table Relationships

### 18.1 Junction Tables (Many-to-Many)

* **Format:** `map_<table1>_<table2>`
* **Ví dụ:**
  * `map_user_roles` (users ↔ roles)
  * `map_plan_features` (plans ↔ features)

### 18.2 Log Tables

* **Format:** `log_<business_concept>`
* **Ví dụ:**
  * `log_api_requests`
  * `log_rate_limits`
  * `log_user_actions`

### 18.3 Temporary Tables

* **Format:** `tmp_<purpose>`
* **Ví dụ:**
  * `tmp_daily_reports`
  * `tmp_batch_processing`

---

## 19. Quy tắc đặt tên cho Performance và Optimization

### 19.1 Partial Indexes

* **Format:** `idx_<table>_<field>_<condition>`
* **Ví dụ:**
  * `idx_users_email_active` (chỉ index users có status = 'active')
  * `idx_orders_amount_paid` (chỉ index orders đã thanh toán)

### 19.2 Expression Indexes

* **Format:** `idx_<table>_<expression>`
* **Ví dụ:**
  * `idx_users_lower_email` (LOWER(email))
  * `idx_orders_date_trunc` (DATE_TRUNC('month', created_at))

---

## 20. Quy tắc đặc biệt cho NestJS + TypeORM + PostgreSQL

### 20.1 BaseEntity Pattern

Tất cả entities phải extend từ `BaseEntityCustom` với các fields:

```typescript
export abstract class BaseEntityCustom extends BaseEntity {
  @PrimaryColumn('bigint')
  id!: string;                    // Snowflake ID

  @Index({ unique: true })
  @Column('uuid', { generated: 'uuid' })
  uuid!: string;                  // UUID cho external references

  @Index()
  @CreateDateColumn({ precision: 6 })
  createdAt!: Date;               // Creation timestamp

  @Index()
  @UpdateDateColumn({ precision: 6 })
  updatedAt!: Date;               // Update timestamp

  @Index()
  @DeleteDateColumn({ precision: 6 })
  deletedAt!: Date | null;        // Soft delete timestamp

  @VersionColumn({ default: 1 })
  version!: number;               // Optimistic locking
}
```

### 20.2 Entity Decorator Patterns

```typescript
@Entity({ name: 'table_name' })  // Explicit table name
@Index(['field1', 'field2'])     // Composite index
@Index(['field'], { unique: true }) // Unique index
export class EntityName extends BaseEntityCustom {
  // Entity fields...
}
```

### 20.3 Column Patterns

```typescript
// Enum columns
@Column({
  type: 'enum',
  enum: CONSTANTS.STATUS,
  default: CONSTANTS.STATUS.ACTIVE,
})
status: StatusType;

// JSONB columns
@Column('jsonb', { nullable: true })
metadata?: Record<string, unknown>;

// Foreign key columns
@Column({ nullable: true })
userId?: string;

@ManyToOne(() => User, { nullable: true })
@JoinColumn({ name: 'userId', referencedColumnName: 'id' })
user: User;
```

### 20.4 Index Patterns cho Performance

```typescript
// Single field index
@Index()
@Column('varchar')
email: string;

// Unique index
@Index({ unique: true })
@Column('varchar')
username: string;

// Composite index
@Index(['status', 'role'])
@Index(['oauthProvider', 'oauthId'], { unique: true })
```

---

## 21. Checklist trước khi commit DB

### 21.1 Naming Conventions
* [ ] Tên theo `snake_case`.
* [ ] Table số nhiều.
* [ ] Field boolean dùng `is_`/`has_`/`can_`.
* [ ] Foreign key theo format `<parent_table>_id`.
* [ ] Index/constraint/sequence/view đặt đúng format.
* [ ] Enum value rõ nghĩa, không viết tắt.

### 21.2 Required Fields
* [ ] Có `created_at`, `updated_at`, `deleted_at`.
* [ ] Có `id` (bigint) và `uuid` (uuid) cho primary key.
* [ ] Có `version` cho optimistic locking.
* [ ] JSONB fields có tên phù hợp (`metadata`, `extra`, `payload`).

### 21.3 Performance & Security
* [ ] Index cho foreign keys.
* [ ] Index cho fields thường query.
* [ ] Sensitive fields (passwords, keys) được hash/encrypt.
* [ ] IP addresses dùng `inet` type cho PostgreSQL.

### 21.4 Data Integrity
* [ ] Constraints đặt đúng tên format.
* [ ] Enum values consistent với business logic.
* [ ] Foreign key relationships đúng.
* [ ] Soft delete fields nullable.

### 21.5 Documentation
* [ ] Comment cho complex fields.
* [ ] Enum values có description.
* [ ] JSONB fields có example structure.

### 21.6 NestJS + TypeORM Specific
* [ ] Entity extends từ `BaseEntityCustom`.
* [ ] Sử dụng decorators đúng cách (`@Entity`, `@Column`, `@Index`).
* [ ] Foreign key relationships với `@ManyToOne`, `@OneToMany`.
* [ ] Enum columns sử dụng constants từ shared folder.
* [ ] JSONB columns có proper typing (`Record<string, unknown>`).

---

## 22. Changelog

### v2.0 (2024-12-19)
* ✅ **Added:** UUID và Snowflake ID patterns
* ✅ **Added:** JSONB field naming conventions
* ✅ **Added:** Soft delete và versioning patterns
* ✅ **Added:** Network và security field patterns
* ✅ **Added:** Audit và logging field patterns
* ✅ **Added:** Enhanced enum naming patterns
* ✅ **Added:** Table relationship naming patterns
* ✅ **Added:** Performance optimization patterns
* ✅ **Added:** NestJS + TypeORM specific patterns
* ✅ **Added:** Comprehensive checklist với 6 categories
* ✅ **Enhanced:** Index naming với composite và foreign key patterns
* ✅ **Enhanced:** Constraint naming với detailed patterns
* ✅ **Enhanced:** Field suffix patterns với 15+ new suffixes

### v1.1 (Previous)
* Basic naming conventions
* Table, field, index, constraint patterns
* Enum và function naming


##### Hãy nhớ: Những quy ước này tồn tại để giúp mã của chúng ta dễ đọc hơn, dễ bảo trì hơn và ít xảy ra lỗi hơn. Khi còn phân vân, hãy ưu tiên sự rõ ràng và nhất quán hơn là sự ngắn gọn. Chúc bạn viết code vui vẻ!
