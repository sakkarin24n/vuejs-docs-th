# สรุป Template Syntax

## Text Interpolation
```html
<!-- แสดงค่าของตัวแปร -->
<span>Message: {{ msg }}</span>
```

## Raw HTML
```html
<!-- แสดง HTML จริง (ระวัง XSS!) -->
<span v-html="rawHtml"></span>
```

## Attribute Bindings
```html
<!-- ใช้ v-bind หรือ : (shorthand) -->
<div :id="dynamicId"></div>
<div v-bind:id="dynamicId"></div>

<!-- Boolean Attributes -->
<button :disabled="isDisabled">Button</button>

<!-- Bind หลาย Attributes -->
<div v-bind="objectOfAttrs"></div>
```

## JavaScript Expressions
```html
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div :id="`list-${id}`"></div>
```

## Directives

### v-if / v-else / v-else-if
```html
<div v-if="type === 'A'">A</div>
<div v-else-if="type === 'B'">B</div>
<div v-else>Not A/B</div>
```

### v-for
```html
<li v-for="item in items" :key="item.id">
  {{ item.message }}
</li>

<!-- มี index -->
<li v-for="(item, index) in items">
  {{ index }} - {{ item.message }}
</li>
```

### v-on (Event Handling)
```html
<!-- @ เป็น shorthand ของ v-on -->
<button @click="doSomething">Click</button>
<button v-on:click="doSomething">Click</button>
```

### v-model (Two-way Binding)
```html
<input v-model="message">
```

### v-bind (:)
```html
<img :src="imageUrl">
<a :href="url">Link</a>
```

## Dynamic Arguments
```html
<a :[attributeName]="url">Link</a>
<a v-bind:[attributeName]="url">Link</a>

<a @[eventName]="handler">Link</a>
```

## Modifiers
```html
<!-- Event Modifiers -->
<form @submit.prevent="onSubmit"></form>
<button @click.stop="doThis"></button>

<!-- Key Modifiers -->
<input @keyup.enter="submit">

<!-- v-model Modifiers -->
<input v-model.lazy="msg">
<input v-model.number="age">
<input v-model.trim="name">
```
