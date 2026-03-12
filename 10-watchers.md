# สรุป Watchers

## ทำไมต้องใช้ Watchers?
- ทำ side effects เมื่อ state เปลี่ยน
- เช่น เรียก API, แก้ไข DOM

## Watch พื้นฐาน

### Options API
```javascript
export default {
  data() {
    return { question: '', answer: '' }
  },
  watch: {
    question(newQuestion, oldQuestion) {
      if (newQuestion.includes('?')) {
        this.getAnswer()
      }
    }
  },
  methods: {
    async getAnswer() {
      // เรียก API...
    }
  }
}
```

### Composition API
```javascript
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('')

watch(question, async (newQuestion) => {
  if (newQuestion.includes('?')) {
    // เรียก API...
  }
})
```

## Deep Watcher

```javascript
// Options API
watch: {
  someObject: {
    handler(newValue) {
      // เรียกเมื่อ nested properties เปลี่ยน
    },
    deep: true
  }
}

// Composition API
watch(obj, () => {
  // เรียกเมื่อ nested properties เปลี่ยน
}, { deep: true })
```

## Immediate Watcher

```javascript
// เรียกทันทีตอน created
watch: {
  question: {
    handler(newQuestion) {
      // เรียกทันที
    },
    immediate: true
  }
}

// Composition API
watch(source, () => {
  // เรียกทันที
}, { immediate: true })
```

## watchEffect()

```javascript
import { ref, watchEffect } from 'vue'

const todoId = ref(1)
const data = ref(null)

watchEffect(async () => {
  // ติดตาม dependencies อัตโนมัติ
  const response = await fetch(`/api/todos/${todoId.value}`)
  data.value = await response.json()
})
```

## watch vs watchEffect

| watch | watchEffect |
|-------|-------------|
| ติดตาม source ที่กำหนด | ติดตามทุกตัวที่ใช้ใน callback |
| เรียกเมื่อ source เปลี่ยน | เรียกทันที + เมื่อ dependencies เปลี่ยน |

## Cleanup

```javascript
import { watch, onWatcherCleanup } from 'vue'

watch(id, (newId) => {
  const controller = new AbortController()
  
  fetch(`/api/${newId}`, { signal: controller.signal })
    .then(...)
  
  onWatcherCleanup(() => {
    controller.abort()
  })
})
```

## หยุด Watcher

```javascript
// กับ Composition API
const unwatch = watchEffect(() => {})

// หยุด
unwatch()
```
