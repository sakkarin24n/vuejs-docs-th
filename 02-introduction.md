# สรุป Vue.js Introduction

## Vue คืออะไร?
Vue เป็น JavaScript Framework สำหรับสร้าง User Interfaces มี 2 คุณสมบัติหลัก:
1. **Declarative Rendering** - สร้าง HTML จาก JavaScript state
2. **Reactivity** - อัพเดต DOM อัตโนมัติเมื่อ state เปลี่ยน

## Single-File Component (SFC)
ไฟล์ `.vue` รวม Logic, Template และ Styles ไว้ในไฟล์เดียว:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">
    Count is: {{ count }}
  </button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

## API Styles

### Options API
```vue
<script>
export default {
  data() {
    return { count: 0 }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    console.log('Component mounted!')
  }
}
</script>
```

### Composition API (แนะนำ)
```vue
<script setup>
import { ref, onMounted } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}

onMounted(() => {
  console.log('Component mounted!')
})
</script>
```

## เลือกใช้ API ไหน?
- **เรียนรู้**: เลือกอันที่เข้าใจง่าย
- **Production**: 
  - ใช้ CDN → Options API
  - ใช้ Build Tools → Composition API + SFC
