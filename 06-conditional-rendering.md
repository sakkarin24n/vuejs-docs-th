# สรุป Conditional Rendering

## v-if / v-else / v-else-if

```html
<h1 v-if="awesome">Vue สุดยอด!</h1>
<h1 v-else>ไม่สิ</h1>

<!-- หลายเงื่อนไข -->
<div v-if="type === 'A'">A</div>
<div v-else-if="type === 'B'">B</div>
<div v-else-if="type === 'C'">C</div>
<div v-else>ไม่ใช่ A/B/C</div>
```

## v-if บน <template>
```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

## v-show
```html
<!-- แสดง/ซ่อน ด้วย CSS display -->
<h1 v-show="ok">Hello!</h1>
```

## v-if vs v-show

| v-if | v-show |
|------|--------|
| ลบ/เพิ่ม element ใน DOM | ใช้ CSS display |
| Lazy (ไม่ render จนกว่าจะ true) | Render เสมอ |
| ทำงานได้กับ v-else | ไม่ทำงานกับ v-else |

## เลือกใช้
- **v-if**: เปลี่ยนแปลงไม่บ่อย, ต้องการ performance ตอน initial render
- **v-show**: สลับบ่อยๆ, ต้องการ performance ตอน toggle
