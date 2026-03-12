# สรุป Lifecycle Hooks

## Lifecycle Diagram

```
create App
  │
  ├─ onMounted / mounted
  │
  ├─ onUpdated / updated  
  │
  └─ onUnmounted / unmounted
```

## Options API Hooks

```javascript
export default {
  // ก่อนสร้าง
  beforeCreate() {
    console.log('beforeCreate')
  },
  created() {
    console.log('created')
  },
  
  // ก่อน mount
  beforeMount() {
    console.log('beforeMount')
  },
  mounted() {
    console.log('mounted - DOM พร้อมแล้ว')
  },
  
  // ก่อน update
  beforeUpdate() {
    console.log('beforeUpdate')
  },
  updated() {
    console.log('updated - DOM อัพเดตแล้ว')
  },
  
  // ก่อน unmount
  beforeUnmount() {
    console.log('beforeUnmount')
  },
  unmounted() {
    console.log('unmounted - ลบออกจาก DOM แล้ว')
  }
}
```

## Composition API Hooks

```javascript
import { 
  onMounted, 
  onUpdated, 
  onUnmounted,
  onBeforeMount,
  onBeforeUpdate,
  onBeforeUnmount
} from 'vue'

onBeforeMount(() => {
  console.log('beforeMount')
})

onMounted(() => {
  console.log('mounted')
})

onUpdated(() => {
  console.log('updated')
})

onBeforeUnmount(() => {
  console.log('beforeUnmount')
})

onUnmounted(() => {
  console.log('unmounted')
})
```

## การใช้งานทั่วไป

| Hook | ใช้ทำอะไร |
|------|-----------|
| mounted | เรียก API, ใช้งาน DOM |
| updated | อัพเดต DOM หลัง data เปลี่ยน |
| unmounted | ล้างข้อมูล, cancel timers |
