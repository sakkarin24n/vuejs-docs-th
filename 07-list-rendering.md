# สรุป List Rendering

## v-for กับ Array

```html
<li v-for="item in items" :key="item.id">
  {{ item.message }}
</li>

<!-- มี index -->
<li v-for="(item, index) in items">
  {{ index }} - {{ item.message }}
</li>

<!-- Destructuring -->
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- ใช้ 'of' แทน 'in' -->
<div v-for="item of items"></div>
```

## v-for กับ Object

```javascript
const myObject = reactive({
  title: 'Vue Guide',
  author: 'Jane Doe'
})
```

```html
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
  
  <!-- มี key และ index -->
  <li v-for="(value, key, index) in myObject">
    {{ index }}. {{ key }}: {{ value }}
  </li>
</ul>
```

## v-for กับ Range

```html
<span v-for="n in 10">{{ n }}</span>
<!-- 1, 2, 3, ... 10 -->
```

## กับ <template>

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

## :key (สำคัญมาก!)
```html
<div v-for="item in items" :key="item.id">
  {{ item.message }}
</div>
```

- ช่วย Vue ติดตาม element
- เพิ่มประสิทธิภาพการ re-render
- ใช้ค่า unique (string หรือ number)

## Array Change Detection

### Mutation Methods (แก้ไข array เดิม)
```javascript
items.push(newItem)
items.pop()
items.shift()
items.unshift(newItem)
items.splice(index, 1)
items.sort()
items.reverse()
```

### Non-mutating Methods (สร้าง array ใหม่)
```javascript
// ต้อง assign คืน
items.value = items.value.filter(item => item.message.match(/Foo/))
```

## Filter/Sort ด้วย Computed

```javascript
const numbers = ref([1, 2, 3, 4, 5])

const evenNumbers = computed(() => {
  return numbers.value.filter(n => n % 2 === 0)
})
```

```html
<li v-for="n in evenNumbers">{{ n }}</li>
```
