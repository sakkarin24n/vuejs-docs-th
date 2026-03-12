# สรุป Reactivity Fundamentals

## ref() - สร้าง Reactive State

### Options API
```javascript
export default {
  data() {
    return {
      count: 1
    }
  },
  mounted() {
    // เข้าถึงผ่าน this
    console.log(this.count)
    this.count = 2
  }
}
```

### Composition API
```javascript
import { ref } from 'vue'

const count = ref(0)

// เข้าถึงค่าผ่าน .value
count.value++

console.log(count.value) // 1
```

### ใช้ใน Template
```vue
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <!-- ไม่ต้องใช้ .value ใน template -->
  <button @click="count++">{{ count }}</button>
</template>
```

## reactive() - Reactive Object

```javascript
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  user: {
    name: 'John'
  }
})

state.count++
state.user.name = 'Jane'
```

## ข้อจำกัดของ reactive()
- ใช้ได้กับ Object/Array เท่านั้น (ไม่ใช้กับ primitive types)
- ไม่สามารถ destructure ได้
- ไม่สามารถ replace object ทั้งหมดได้

```javascript
// ❌ ไม่ถูก
const { count } = state
count++

// ✅ ถูก
const count = ref(0) // แนะนำใช้ ref() แทน
```

## nextTick() - รอ DOM Update

```javascript
import { ref, nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // ตอนนี้ DOM อัพเดตแล้ว
}
```

## Deep Reactivity
Vue ทำให้ nested objects/arrays เป็น reactive อัตโนมัติ

```javascript
const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

obj.value.nested.count++
obj.value.arr.push('baz')
```
