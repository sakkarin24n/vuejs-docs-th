# สรุป Vue.js Quick Start

## การติดตั้ง Vue Project

```bash
# สร้าง Project ใหม่
npm create vue@latest
# หรือ
pnpm create vue@latest

# ติดตั้ง dependencies
npm install

# รัน Development Server
npm run dev

# Build สำหรับ Production
npm run build
```

## การใช้ Vue ผ่าน CDN

```html
<!-- ใช้ Vue ผ่าน CDN แบบ Global Build -->
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app">{{ message }}</div>

<script>
  const { createApp, ref } = Vue
  
  createApp({
    setup() {
      const message = ref('Hello Vue!')
      return { message }
    }
  }).mount('#app')
</script>
```

## ใช้ Vue ผ่าน ES Modules

```html
<script type="importmap">
{
  "imports": {
    "vue": "https://unpkg.com/vue@3/dist/vue.esm-browser.js"
  }
}
</script>

<script type="module">
import { createApp, ref } from 'vue'

createApp({
  setup() {
    const message = ref('Hello Vue!')
    return { message }
  }
}).mount('#app')
</script>
```

## IDE แนะนำ
- VS Code + Vue - Official Extension
