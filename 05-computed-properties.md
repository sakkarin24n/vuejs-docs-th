# สรุป Computed Properties

## ทำไมต้องใช้ Computed?
ใช้สำหรับ logic ที่ซับซ้อนใน template ให้อ่านง่ายและมี caching

## ตัวอย่างพื้นฐาน

### Options API
```javascript
export default {
  data() {
    return {
      author: {
        books: ['Vue 2', 'Vue 3']
      }
    }
  },
  computed: {
    publishedBooksMessage() {
      return this.author.books.length > 0 ? 'Yes' : 'No'
    }
  }
}
```

### Composition API
```javascript
import { reactive, computed } from 'vue'

const author = reactive({
  books: ['Vue 2', 'Vue 3']
})

const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
```

## Computed vs Methods

```javascript
// Computed - มี caching (คำนวณเมื่อ dependencies เปลี่ยน)
const computedValue = computed(() => {
  return Date.now() // ไม่เคยอัพเดต!
})

// Method - คำนวณใหม่ทุกครั้งที่ render
function methodValue() {
  return Date.now()
}
```

## Writable Computed

```javascript
const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  get() {
    return firstName.value + ' ' + lastName.value
  },
  set(newValue) {
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})

fullName.value = 'Jane Doe' // จะอัพเดต firstName และ lastName
```

## Best Practices
- Getter ต้องไม่มี side effects (async, mutation, DOM)
- อย่า mutate computed value - ให้ update source state แทน
