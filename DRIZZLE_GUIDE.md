# 📘 Drizzle ORM Guide (PostgreSQL)
### Schema + Field Types + Relations + Usage

คู่มือนี้แปลง Prisma Schema → Drizzle ORM (pg-core) พร้อมอธิบายแต่ละส่วนให้เข้าใจง่าย  
อ่านไฟล์เดียวจบ เห็นภาพตั้งแต่ Schema → Relations → ใช้งานจริง

---

## 1️⃣ โครงสร้างพื้นฐาน

```
pgTable("users", { ... })
  │
  ├── ชื่อ table ใน PostgreSQL
  └── แต่ละ field = column ใน table
```

| Prisma | Drizzle |
|---|---|
| `model User { ... }` | `pgTable("users", { ... })` |
| `@relation(...)` | `relations(...)` |
| `@id @default(autoincrement())` | `serial("id").primaryKey()` |
| `@default(now())` | `.defaultNow()` |
| `@unique` | `.unique()` |
| `enum Role { USER ADMIN }` | `varchar("role").default("USER")` |

---

## 2️⃣ Field Types ที่ใช้บ่อย

| ฟังก์ชัน | ชนิดข้อมูล | ตัวอย่าง |
|---|---|---|
| `serial()` | Auto-increment int | ID |
| `varchar(n)` | String จำกัดความยาว | email, name |
| `text()` | String ยาวไม่จำกัด | description, address |
| `integer()` | จำนวนเต็ม | price, stock |
| `boolean()` | true / false | active, approved |
| `timestamp()` | วันที่และเวลา | createdAt |
| `uuid()` | UUID สุ่ม | token |
| `json()` | JSON object | selectedOptions |

### Options ที่ใช้ต่อท้าย Field

| Option | ความหมาย |
|---|---|
| `.notNull()` | ห้ามเป็น null |
| `.unique()` | ห้ามซ้ำในตาราง |
| `.default(value)` | ค่า default เมื่อไม่ได้ส่งมา |
| `.defaultNow()` | default = เวลาปัจจุบัน |
| `.primaryKey()` | เป็น primary key |

---

## 3️⃣ Full Schema

```ts
// db/schema.ts
import {
  pgTable, serial, varchar, text,
  integer, boolean, timestamp, json,
} from "drizzle-orm/pg-core"
import { relations } from "drizzle-orm"

// ─────────────────────────────────────────────
// USER
// ─────────────────────────────────────────────
export const users = pgTable("users", {
  id:        serial("id").primaryKey(),
  email:     varchar("email", { length: 256 }).notNull().unique(),
  password:  varchar("password", { length: 256 }).notNull(),
  name:      varchar("name", { length: 128 }),
  address:   text("address"),
  role:      varchar("role", { length: 32 }).default("USER"), // "USER" | "ADMIN"
  createdAt: timestamp("created_at").defaultNow(),
  updatedAt: timestamp("updated_at").defaultNow(),
})

// ─────────────────────────────────────────────
// PRODUCT
// ─────────────────────────────────────────────
export const products = pgTable("products", {
  id:          serial("id").primaryKey(),
  name:        varchar("name", { length: 256 }).notNull(),
  description: text("description"),
  basePrice:   integer("base_price").notNull(),
  active:      boolean("active").default(true),
  mainImage:   text("main_image").notNull(),
  stock:       integer("stock").default(0),
  createdAt:   timestamp("created_at").defaultNow(),
  updatedAt:   timestamp("updated_at").defaultNow(),
})

// รูปภาพเพิ่มเติมของสินค้า
export const productImages = pgTable("product_images", {
  id:        serial("id").primaryKey(),
  productId: integer("product_id").notNull(),  // FK → products.id
  url:       text("url").notNull(),
  isMain:    boolean("is_main").default(false),
})

// ตัวเลือกเพิ่มเติมของสินค้า (สี, RAM, ฯลฯ)
export const productOptions = pgTable("product_options", {
  id:          serial("id").primaryKey(),
  productId:   integer("product_id").notNull(),  // FK → products.id
  name:        varchar("name", { length: 128 }).notNull(),
  description: text("description"),
  extraPrice:  integer("extra_price").default(0),
})

// ─────────────────────────────────────────────
// CART
// ─────────────────────────────────────────────
export const carts = pgTable("carts", {
  id:     serial("id").primaryKey(),
  userId: integer("user_id").notNull(),  // FK → users.id
})

// สินค้าแต่ละชิ้นใน cart
export const cartItems = pgTable("cart_items", {
  id:              serial("id").primaryKey(),
  cartId:          integer("cart_id").notNull(),        // FK → carts.id
  productId:       integer("product_id").notNull(),     // FK → products.id
  selectedOptions: json("selected_options").notNull(),  // { color: "black", ram: "16GB" }
  quantity:        integer("quantity").default(1),
  finalPrice:      integer("final_price").notNull(),
})

// ─────────────────────────────────────────────
// ORDER
// ─────────────────────────────────────────────
export const orders = pgTable("orders", {
  id:        serial("id").primaryKey(),
  userId:    integer("user_id").notNull(),  // FK → users.id
  status:    varchar("status", { length: 32 }).default("PENDING"),
  approved:  boolean("approved").default(false),
  createdAt: timestamp("created_at").defaultNow(),
  updatedAt: timestamp("updated_at").defaultNow(),
})

// สินค้าแต่ละชิ้นใน order
export const orderItems = pgTable("order_items", {
  id:              serial("id").primaryKey(),
  orderId:         integer("order_id").notNull(),       // FK → orders.id
  productId:       integer("product_id").notNull(),     // FK → products.id
  selectedOptions: json("selected_options").notNull(),
  quantity:        integer("quantity").default(1),
  finalPrice:      integer("final_price").notNull(),
})

// ─────────────────────────────────────────────
// REFRESH TOKEN
// ─────────────────────────────────────────────
export const refreshTokens = pgTable("refresh_tokens", {
  id:        serial("id").primaryKey(),
  userId:    integer("user_id").notNull(),            // FK → users.id
  token:     varchar("token", { length: 512 }).notNull().unique(),
  createdAt: timestamp("created_at").defaultNow(),
  expiresAt: timestamp("expires_at").notNull(),
})
```

---

## 4️⃣ Relations

> `relations()` ใช้บอก Drizzle ว่า table ไหนเชื่อมกับอะไร  
> **ไม่ได้สร้าง foreign key จริงใน DB** — แต่ช่วยให้ query ด้วย `with:` ได้

### ความสัมพันธ์ที่มีในระบบ

```
users ──────< orders
users ──────< carts
users ──────< refresh_tokens
products ───< product_images
products ───< product_options
products ───< cart_items
products ───< order_items
carts ──────< cart_items
orders ─────< order_items
```

### Relations Code

```ts
// USER: มีหลาย orders, carts, refreshTokens
export const usersRelations = relations(users, ({ many }) => ({
  orders:        many(orders),
  carts:         many(carts),
  refreshTokens: many(refreshTokens),
}))

// PRODUCT: มีหลาย images, options, cartItems, orderItems
export const productsRelations = relations(products, ({ many }) => ({
  images:     many(productImages),
  options:    many(productOptions),
  cartItems:  many(cartItems),
  orderItems: many(orderItems),
}))

// PRODUCT IMAGE: ของ product หนึ่ง
export const productImagesRelations = relations(productImages, ({ one }) => ({
  product: one(products, {
    fields:     [productImages.productId],
    references: [products.id],
  }),
}))

// PRODUCT OPTION: ของ product หนึ่ง
export const productOptionsRelations = relations(productOptions, ({ one }) => ({
  product: one(products, {
    fields:     [productOptions.productId],
    references: [products.id],
  }),
}))

// CART: ของ user หนึ่ง, มีหลาย cartItems
export const cartsRelations = relations(carts, ({ one, many }) => ({
  user:  one(users, {
    fields:     [carts.userId],
    references: [users.id],
  }),
  items: many(cartItems),
}))

// CART ITEM: ของ cart หนึ่ง + product หนึ่ง
export const cartItemsRelations = relations(cartItems, ({ one }) => ({
  cart: one(carts, {
    fields:     [cartItems.cartId],
    references: [carts.id],
  }),
  product: one(products, {
    fields:     [cartItems.productId],
    references: [products.id],
  }),
}))

// ORDER: ของ user หนึ่ง, มีหลาย orderItems
export const ordersRelations = relations(orders, ({ one, many }) => ({
  user:  one(users, {
    fields:     [orders.userId],
    references: [users.id],
  }),
  items: many(orderItems),
}))

// ORDER ITEM: ของ order หนึ่ง + product หนึ่ง
export const orderItemsRelations = relations(orderItems, ({ one }) => ({
  order: one(orders, {
    fields:     [orderItems.orderId],
    references: [orders.id],
  }),
  product: one(products, {
    fields:     [orderItems.productId],
    references: [products.id],
  }),
}))

// REFRESH TOKEN: ของ user หนึ่ง
export const refreshTokensRelations = relations(refreshTokens, ({ one }) => ({
  user: one(users, {
    fields:     [refreshTokens.userId],
    references: [users.id],
  }),
}))
```

---

## 5️⃣ ตัวอย่างการใช้งานจริง

### Insert

```ts
// เพิ่ม user ใหม่
await db.insert(users).values({
  email:    "user@example.com",
  password: "hashed_pw",
  name:     "Sakkarin",
  role:     "ADMIN",
})

// เพิ่มสินค้าในตะกร้า
await db.insert(cartItems).values({
  cartId:          1,
  productId:       5,
  selectedOptions: { color: "black", storage: "256GB" },
  quantity:        2,
  finalPrice:      69000,
})
```

### Query + Relations

```ts
// ดึง user พร้อม orders และ carts
const result = await db.query.users.findMany({
  with: {
    orders: true,
    carts:  true,
  },
})

// ดึง order พร้อม items และ product ของแต่ละ item
const orderDetail = await db.query.orders.findFirst({
  where: eq(orders.id, 1),
  with: {
    items: {
      with: { product: true },
    },
  },
})
```

### Update

```ts
// อัปเดตสถานะ order
await db
  .update(orders)
  .set({ status: "SHIPPED", approved: true })
  .where(eq(orders.id, 1))
```

### Delete

```ts
// ลบ cartItem
await db
  .delete(cartItems)
  .where(eq(cartItems.id, 3))
```

---

## 6️⃣ เทียบ Prisma vs Drizzle แบบเต็ม

| หัวข้อ | Prisma | Drizzle |
|---|---|---|
| Schema format | `.prisma` DSL | TypeScript |
| Table definition | `model User { }` | `pgTable("users", { })` |
| Primary key | `@id @default(autoincrement())` | `serial().primaryKey()` |
| Unique | `@unique` | `.unique()` |
| Default now | `@default(now())` | `.defaultNow()` |
| Enum | `enum Role { USER ADMIN }` | `varchar().default("USER")` |
| Relation | `@relation(fields, references)` | `relations()` แยกไฟล์ |
| Query | `prisma.user.findMany()` | `db.query.users.findMany()` |
| Join | `include: { orders: true }` | `with: { orders: true }` |
| Migration | `prisma migrate dev` | `drizzle-kit generate` |

---

## 7️⃣ ข้อสังเกตสำคัญ

**`updatedAt` ใน Drizzle**  
Prisma มี `@updatedAt` อัตโนมัติ แต่ Drizzle ไม่มี — ต้องจัดการเอง

```ts
// ต้องส่ง updatedAt ทุกครั้งที่ update
await db
  .update(products)
  .set({ name: "iPhone 16", updatedAt: new Date() })
  .where(eq(products.id, 1))
```

**Foreign Key จริงใน DB**  
`relations()` ใน Drizzle เป็นแค่ metadata สำหรับ query  
ถ้าต้องการ FK constraint จริงต้องเพิ่ม `.references()`:

```ts
productId: integer("product_id")
  .notNull()
  .references(() => products.id, { onDelete: "cascade" }),
```

---

## 8️⃣ สรุปภาพรวม

```
Schema (db/schema.ts)
  │
  ├── pgTable()       → กำหนด table + columns
  ├── Field types     → serial, varchar, text, integer, boolean, timestamp, json
  ├── Options         → .notNull(), .unique(), .default(), .defaultNow()
  └── relations()     → map foreign key สำหรับ query with:

Query
  ├── db.insert()     → เพิ่มข้อมูล
  ├── db.query.*      → ดึงข้อมูล (รองรับ with:)
  ├── db.update()     → แก้ไขข้อมูล
  └── db.delete()     → ลบข้อมูล
```

> **Drizzle = TypeScript-first ORM**  
> Schema คือ TypeScript จริง ๆ → Type-safe 100% ตั้งแต่ Schema ถึง Query 🚀
