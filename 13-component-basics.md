# สรุป Component Basics

## สร้าง Component

### Single-File Component (.vue)
```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">
    Clicked {{ count }} times
  </button>
</template>
```

### ไม่ใช้ Build Tool
```javascript
export default {
  data() {
    return { count: 0 }
  },
  template: `<button @click="count++">Click</button>`
}
```

## ใช้ Component

```vue
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <ButtonCounter />
  <ButtonCounter />
  <ButtonCounter />
</template>
```

## Props - ส่งข้อมูลไป Component ลูก

```vue
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

```vue
<!-- Parent -->
<BlogPost title="My Journey" />
<BlogPost :title="post.title" />
```

## Events - รับข้อมูลจาก Component ลูก

```vue
<!-- Child: BlogPost.vue -->
<script setup>
const emit = defineEmits(['enlarge-text'])
</script>

<template>
  <button @click="$emit('enlarge-text')">
    Enlarge text
  </button>
</template>
```

```vue
<!-- Parent -->
<BlogPost @enlarge-text="postFontSize += 0.1" />
```

## Slots - ใส่เนื้อหาใน Component

```vue
<!-- AlertBox.vue -->
<template>
  <div class="alert-box">
    <strong>Error!</strong>
    <slot></slot>
  </div>
</template>
```

```vue
<!-- ใช้งาน -->
<AlertBox>
  Something bad happened.
</AlertBox>
```

## Dynamic Components

```vue
<component :is="currentTab"></component>

<!-- หรือ -->
<component :is="tabs[currentTab]"></component>
```

## in-DOM Templates

```html
<!-- ใช้ kebab-case -->
<button-counter></button-counter>
<blog-post post-title="Hello"></blog-post>
```
