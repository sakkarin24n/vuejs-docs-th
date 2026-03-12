# สรุป Template Refs

## Template Refs คืออะไร?
ใช้เข้าถึง DOM elements โดยตรง หลังจาก mounted

## ใช้งาน

### Composition API (Vue 3.5+)
```vue
<script setup>
import { useTemplateRef, onMounted } from 'vue'

const myInput = useTemplateRef('my-input')

onMounted(() => {
  myInput.value.focus()
})
</script>

<template>
  <input ref="my-input">
</template>
```

### Composition API (ก่อน 3.5)
```vue
<script setup>
import { ref, onMounted } from 'vue'

const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input">
</template>
```

### Options API
```vue
<script>
export default {
  mounted() {
    this.$refs.input.focus()
  }
}
</script>

<template>
  <input ref="input">
</template>
```

## Ref กับ Component

```vue
<!-- Parent.vue -->
<script setup>
import { useTemplateRef, onMounted } from 'vue'
import Child from './Child.vue'

const childRef = useTemplateRef('child')

onMounted(() => {
  // เข้าถึง child component instance
  childRef.value.someMethod()
})
</script>

<template>
  <Child ref="child">
</template>
```

### Child (script setup) ต้อง expose

```vue
<!-- Child.vue -->
<script setup>
const a = 1
const b = ref(2)

defineExpose({ a, b })
</script>
```

## v-for กับ Ref

```vue
<script setup>
import { ref, useTemplateRef, onMounted } from 'vue'

const list = ref([1, 2, 3])
const itemRefs = useTemplateRef('items')

onMounted(() => {
  console.log(itemRefs.value) // [el, el, el]
})
</script>

<template>
  <li v-for="item in list" ref="items">
    {{ item }}
  </li>
</template>
```

## Function Ref

```html
<input :ref="el => { inputEl = el }">
```
