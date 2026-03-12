# Vue.js Docs Summary (ภาษาไทย)

## 📚 สารบัญ

1. [Quick Start](01-quick-start.md) - การติดตั้งและเริ่มต้นใช้งาน
2. [Introduction](02-introduction.md) - รู้จัก Vue, SFC, Options/Composition API
3. [Template Syntax](03-template-syntax.md) - Template syntax, Directives, Modifiers
4. [Reactivity Fundamentals](04-reactivity-fundamentals.md) - ref(), reactive(), nextTick
5. [Computed Properties](05-computed-properties.md) - Computed, Getter/Setter
6. [Conditional Rendering](06-conditional-rendering.md) - v-if, v-show
7. [List Rendering](07-list-rendering.md) - v-for, key, Array methods
8. [Event Handling](08-event-handling.md) - @click, Modifiers
9. [Forms](09-forms.md) - v-model, Input bindings
10. [Watchers](10-watchers.md) - watch, watchEffect
11. [Template Refs](11-template-refs.md) - เข้าถึง DOM โดยตรง
12. [Lifecycle Hooks](12-lifecycle-hooks.md) - mounted, updated, unmounted
13. [Component Basics](13-component-basics.md) - Props, Events, Slots

---

## 🔑 แนวคิดสำคัญ

### Reactive State
```javascript
// ref() - ใช้ได้กับทุก type
const count = ref(0)
count.value++

// reactive() - ใช้กับ Object
const state = reactive({ count: 0 })
state.count++
```

### Template
```html
<!-- Data binding -->
{{ message }}
<input :value="message">

<!-- Event -->
<button @click="handleClick">

<!-- Two-way -->
<input v-model="message">

<!-- Condition -->
<div v-if="show">...</div>

<!-- Loop -->
<li v-for="item in items" :key="item.id">
```

### Component
```vue
<script setup>
defineProps(['title'])
const emit = defineEmits(['update'])
</script>

<template>
  <Child :data="value" @event="handle">
    <slot></slot>
  </Child>
</template>
```

---

## 🚀 เริ่มต้นเร็ว

```bash
# สร้าง Project
npm create vue@latest

# รัน Dev Server
npm run dev

# Build Production
npm run build
```
