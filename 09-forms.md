# สรุป Form Input Bindings (v-model)

## v-model พื้นฐาน

```html
<!-- Text -->
<input v-model="message">
<p>Message: {{ message }}</p>

<!-- Multiline Text -->
<textarea v-model="message"></textarea>
```

## Checkbox

```html
<!-- Boolean -->
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>

<!-- Array -->
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="jack">Jack</label>
<label for="john">John</label>
<p>Checked: {{ checkedNames }}</p>
```

## Radio

```html
<input type="radio" id="one" value="One" v-model="picked">
<input type="radio" id="two" value="Two" v-model="picked">
<p>Picked: {{ picked }}</p>
```

## Select

```html
<!-- Single -->
<select v-model="selected">
  <option disabled value="">Please select</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>

<!-- Multiple -->
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>

<!-- Dynamic Options -->
<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>
```

## Value Bindings

```html
<!-- Checkbox with custom values -->
<input type="checkbox" v-model="toggle" true-value="yes" false-value="no">

<!-- Radio with v-bind -->
<input type="radio" v-model="pick" :value="first">
<input type="radio" v-model="pick" :value="second">

<!-- Select with object -->
<option :value="{ number: 123 }">123</option>
```

## Modifiers

```html
<!-- sync หลัง change event (ไม่ใช่ input) -->
<input v-model.lazy="msg">

<!-- แปลงเป็น number -->
<input v-model.number="age">

<!-- trim whitespace -->
<input v-model.trim="name">
```
