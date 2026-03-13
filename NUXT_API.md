# 📘 Nuxt 3 Full-Stack Guide
### Frontend + Backend + Auth + CRUD + DB

คู่มือนี้สรุป **โครงสร้างและการทำงานของ Nuxt 3 แบบ Full-stack** ตั้งแต่  
Frontend → API → Authentication → Database → CRUD  
อ่านไฟล์เดียวจบ จะเห็นภาพการไหลของข้อมูลทั้งหมดในระบบ

---

## 1️⃣ แนวคิดพื้นฐาน (Frontend vs Backend)

ลองนึกภาพเป็นระบบ **ส่งจดหมาย**

| ส่วน | เปรียบเทียบ |
|---|---|
| Frontend | คนส่งจดหมาย |
| Backend API | พนักงานรับจดหมาย |
| Database | ลิ้นชักเก็บเอกสาร |
| HTTP Request | ซองจดหมาย |
| HTTP Response | จดหมายตอบกลับ |

---

## 2️⃣ ตัวอย่างการส่งข้อมูล POST

### Backend — `server/api/items.post.ts`

```ts
export default defineEventHandler(async (event) => {
  // event = ซองจดหมายทั้งหมด
  const body = await readBody(event)

  // บันทึกลง DB
  const result = await db.save(body)

  // ส่งผลกลับ
  return result
})
```

### Frontend — `pages/register.vue`

```vue
<script setup>
const submitData = async () => {
  const { data } = await useFetch('/api/items', {
    method: 'POST',
    body: {
      name: 'iPhone 15',
      price: 30000
    }
  })
  console.log(data.value)
}
</script>
```

---

## 3️⃣ คำศัพท์สำคัญ

| คำ | ความหมาย |
|---|---|
| `event` | object ที่เก็บ request ทั้งหมด |
| `readBody` | อ่าน JSON ที่ user ส่งมา |
| `body` | ข้อมูลที่ client ส่ง |
| `return` | ส่ง response กลับ frontend |

---

## 4️⃣ หลักการ POST

**ใช้กับ:** Register, Login, Add to Cart, Create Post, Upload Data

**Flow:**
```
Frontend  →  POST  →  Backend
Backend   →  readBody  →  Database
Database  →  result  →  return
Frontend  ←  response
```

---

## 5️⃣ Server Engine (Backend Setup)

สร้าง utility ใน `server/utils/`

### Prisma Instance — `server/utils/prisma.ts`

```ts
import { PrismaClient } from '@prisma/client'

export const prisma = new PrismaClient()
```

---

## 6️⃣ ระบบ Auth Helper — `server/utils/auth.ts`

```ts
import argon2 from 'argon2'
import jwt from 'jsonwebtoken'

export const hashPassword = (pw: string) =>
  argon2.hash(pw)

export const verifyPassword = (hash: string, pw: string) =>
  argon2.verify(hash, pw)

export const signToken = (payload: object) => {
  const config = useRuntimeConfig()
  return jwt.sign(payload, config.jwtSecret, {
    expiresIn: '1d'
  })
}

export const verifyToken = (token: string) => {
  const config = useRuntimeConfig()
  try {
    return jwt.verify(token, config.jwtSecret)
  } catch {
    return null
  }
}
```

---

## 7️⃣ Register API — `server/api/auth/register.post.ts`

```ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event)
  const hashed = await hashPassword(body.password)

  return await prisma.user.create({
    data: {
      email: body.email,
      password: hashed
    }
  })
})
```

---

## 8️⃣ Login API — `server/api/auth/login.post.ts`

```ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event)

  const user = await prisma.user.findUnique({
    where: { email: body.email }
  })

  if (!user || !(await verifyPassword(user.password, body.password))) {
    throw createError({
      statusCode: 401,
      message: 'Invalid credentials'
    })
  }

  const token = signToken({ userId: user.id })
  return { token }
})
```

---

## 9️⃣ เก็บ Token ใน Frontend — `pages/login.vue`

```vue
<script setup>
const login = async () => {
  const res = await $fetch('/api/auth/login', {
    method: 'POST',
    body: form
  })

  const cookie = useCookie('auth_token', {
    maxAge: 60 * 60 * 24
  })

  cookie.value = res.token
  await navigateTo('/dashboard')
}
</script>
```

---

## 🔟 Route Middleware — `middleware/auth.ts`

```ts
export default defineNuxtRouteMiddleware(() => {
  const token = useCookie('auth_token')

  if (!token.value) {
    return navigateTo('/login')
  }
})
```

---

## 1️⃣1️⃣ Product API (CRUD)

### GET — `server/api/products/index.get.ts`

```ts
export default defineEventHandler(async () => {
  return await prisma.product.findMany({
    orderBy: { createdAt: 'desc' }
  })
})
```

### POST — `server/api/products/index.post.ts`

```ts
export default defineEventHandler(async (event) => {
  const body = await readBody(event)

  return await prisma.product.create({
    data: {
      name: body.name,
      price: Number(body.price),
      options: body.options
    }
  })
})
```

### PUT — `server/api/products/[id].put.ts`

```ts
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')
  const body = await readBody(event)

  return await prisma.product.update({
    where: { id: Number(id) },
    data: body
  })
})
```

### DELETE — `server/api/products/[id].delete.ts`

```ts
export default defineEventHandler(async (event) => {
  const id = getRouterParam(event, 'id')

  await prisma.product.delete({
    where: { id: Number(id) }
  })

  return { success: true }
})
```

---

## 1️⃣2️⃣ Frontend Product List — `pages/products/index.vue`

```vue
<script setup>
const { data: products, refresh } = await useFetch('/api/products')

const deleteItem = async (id) => {
  await $fetch(`/api/products/${id}`, {
    method: 'DELETE'
  })
  refresh()
}
</script>

<template>
  <div v-for="p in products" :key="p.id">
    {{ p.name }} - {{ p.price }}
    <button @click="deleteItem(p.id)">Delete</button>
  </div>
</template>
```

---

## 1️⃣3️⃣ Add Product — `pages/products/manage.vue`

```vue
<script setup>
const form = reactive({
  name: '',
  price: 0
})

const addProduct = async () => {
  await $fetch('/api/products', {
    method: 'POST',
    body: form
  })
  await navigateTo('/products')
}
</script>
```

---

## 1️⃣4️⃣ Flow ของ CRUD

```
Frontend
   ↓
HTTP Request
   ↓
Nuxt API
   ↓
Prisma
   ↓
Database
   ↓
Response
   ↓
Frontend UI Update
```

---

## 1️⃣5️⃣ useFetch vs $fetch

| function | ใช้เมื่อ |
|---|---|
| `useFetch` | โหลดข้อมูลตอนเปิดหน้า |
| `$fetch` | ส่งข้อมูลจาก event |

---

## 1️⃣6️⃣ Cookie vs LocalStorage

| Storage | ใช้กับ |
|---|---|
| Cookie | Auth token |
| LocalStorage | Cart / large data |

---

## 1️⃣7️⃣ Dynamic Route

**URL:** `/api/products/10`

**Backend:**
```ts
const id = getRouterParam(event, 'id')
```

---

## 1️⃣8️⃣ Security Best Practice

| สิ่ง | วิธี |
|---|---|
| Password | Hash ด้วย Argon2 |
| Auth | JWT |
| Token | Cookie |
| Secret | `.env` |

---

## 1️⃣9️⃣ Environment Variables — `.env`

```env
DATABASE_URL=mysql://...
JWT_SECRET=super_secret_key
```

---

## 2️⃣0️⃣ Project Structure

```
project/
├── server/
│   ├── api/
│   │   ├── auth/
│   │   │   ├── register.post.ts
│   │   │   └── login.post.ts
│   │   └── products/
│   │       ├── index.get.ts
│   │       ├── index.post.ts
│   │       ├── [id].put.ts
│   │       └── [id].delete.ts
│   └── utils/
│       ├── prisma.ts
│       └── auth.ts
├── middleware/
│   └── auth.ts
├── pages/
│   ├── login.vue
│   ├── register.vue
│   ├── dashboard.vue
│   └── products/
└── .env
```

---

## 🚀 Full Flow Example

### Register
```
Frontend  →  POST /api/auth/register
Backend   →  Hash password
Backend   →  Save DB
```

### Login
```
Frontend  →  POST /api/auth/login
Backend   →  Verify password
Backend   →  Create JWT
Frontend  →  Save cookie
```

### Access Page
```
Middleware  →  Check cookie
            →  Allow / Deny access
```

---

## 🎯 Final Summary

**Nuxt 3 Full-stack Stack**

| Layer | Technology |
|---|---|
| Framework | Nuxt 3 + Vue |
| Server API | Nuxt Server Routes |
| ORM | Prisma |
| Database | MySQL / PostgreSQL |
| Auth | JWT + Argon2 |
| Session | Cookie |

**Use Cases:** SaaS · E-commerce · Dashboard · CMS · API Server

> **Nuxt 3 สามารถทำ Frontend + Backend + Auth + DB ใน repo เดียว**  
> ถือเป็น Modern Full-stack Framework 🚀
