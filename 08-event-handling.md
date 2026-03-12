# สรุป Event Handling

## v-on Directive
```html
<!-- @ เป็น shorthand -->
<button @click="handleClick">Click</button>
<button v-on:click="handleClick">Click</button>
```

## Inline Handlers
```javascript
const count = ref(0)
```

```html
<button @click="count++">Add 1</button>
<p>Count: {{ count }}</p>
```

## Method Handlers
```javascript
function greet(event) {
  alert(`Hello ${event.target.tagName}!`)
}
```

```html
<button @click="greet">Greet</button>
```

## เรียก Method พร้อม Arguments
```javascript
function say(message) {
  alert(message)
}
```

```html
<button @click="say('hello')">Say hello</button>
<button @click="say('bye')">Say bye</button>
```

## เข้าถึง $event
```html
<!-- ใช้ $event variable -->
<button @click="warn('Form error', $event)">Submit</button>

<!-- หรือ arrow function -->
<button @click="(event) => warn('Form error', event)">Submit</button>
```

## Event Modifiers

```html
<!-- หยุดการ propagation -->
<a @click.stop="doThis"></a>

<!-- ป้องกัน default behavior -->
<form @submit.prevent="onSubmit"></form>

<!-- chain modifiers -->
<a @click.stop.prevent="doThat"></a>

<!-- trigger เมื่อ event เกิดกับ element เอง -->
<div @click.self="doThis"></div>
```

## Key Modifiers

```html
<!-- กด Enter เท่านั้น -->
<input @keyup.enter="submit">

<!-- ชื่ออื่นๆ -->
@keyup.tab
@keyup.delete
@keyup.esc
@keyup.space
@keyup.up
@keyup.down
@keyup.left
@keyup.right
```

## System Modifiers

```html
<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">A</div>

<!-- Exact - กด Ctrl อย่างเดียว -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

## Mouse Button Modifiers
```html
<div @click.left="leftClick"></div>
<div @click.right="rightClick"></div>
<div @click.middle="middleClick"></div>
```
