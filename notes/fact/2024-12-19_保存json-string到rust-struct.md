---
date: 2024-12-19
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# 保存json string到rust struct

如果我要從 vue 中使用 rust 建立的 struct 去保存 json string, 該 struct 在 instantiate with 該 json string 時會將其轉換為 object 保存在 instance 中


## 範例專案架構

```bash
~/D/g/s/r/vue3-webpack5-wasm-project > tree -I 'node_modules|target'
.
├── package.json
├── package-lock.json
├── postcss.config.js
├── public
│   └── index.html
├── rust_lib
│   ├── Cargo.lock
│   ├── Cargo.toml
│   ├── pkg  # cp -r pkg ../src/wasm 的方式手動複製到 vue src 下
│   │   ├── package.json
│   │   ├── rust_lib_bg.wasm
│   │   ├── rust_lib_bg.wasm.d.ts
│   │   ├── rust_lib.d.ts
│   │   └── rust_lib.js
│   └── src
│       └── lib.rs
├── src
│   ├── App.vue
│   ├── main.ts
│   ├── shims-vue.d.ts
│   ├── styles
│   │   └── main.css
│   └── wasm
│       ├── package.json
│       ├── rust_lib_bg.wasm
│       ├── rust_lib_bg.wasm.d.ts
│       ├── rust_lib.d.ts
│       └── rust_lib.js
├── tailwind.config.js
├── tsconfig.json
└── webpack.config.js
```

## Cargo.toml

```toml
[package]
name = "rust_lib"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
wasm-bindgen = "0.2"
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
serde-wasm-bindgen = "0.6"

```

## lib.rs

```rust
use wasm_bindgen::prelude::*;
use serde_wasm_bindgen;

#[wasm_bindgen]
pub struct JsonHolder {
    data: serde_json::Value,
}

#[wasm_bindgen]
impl JsonHolder {
    #[wasm_bindgen(constructor)]
    pub fn new(json_str: &str) -> Result<JsonHolder, JsValue> {
        let data: serde_json::Value = serde_json::from_str(json_str)
            .map_err(|e| JsValue::from_str(&format!("Failed to parse JSON: {}", e)))?;
        Ok(JsonHolder { data })
    }

    pub fn get_value(&self, key: &str) -> JsValue {
        match self.data.get(key) {
            Some(value) => serde_wasm_bindgen::to_value(value).unwrap(),
            None => JsValue::NULL,
        }
    }
}
```

## 編譯並cp到vue專案src

```bash
cd rust_lib
wasm-pack build --target web
cp -r pkg ../src/wasm
```

## Vue 組件中使用 Rust 創建的結構體(以 App.vue 為例)

```vue
<template>
  <div>
    <h1>JSON Value: {{ jsonValue }}</h1>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import init, { JsonHolder } from '../rust_lib/pkg/rust_lib';

export default defineComponent({
  name: 'App',
  setup() {
    const jsonValue = ref('');

    onMounted(async () => {
      await init();
      
      const jsonString = '{"name": "John", "age": 30}';
      const holder = new JsonHolder(jsonString);
      
      jsonValue.value = holder.get_value('name') as string;
    });

    return {
      jsonValue
    };
  }
});
</script>
```

## Webpack 配置

```javascript
module.exports = {
  // ... 其他配置
  experiments: {
    asyncWebAssembly: true,
  },
  // ... 其他配置
};
```


## Notes

需要注意的幾點：

1. 這個例子中，我們使用了 `serde_json::Value` 來存儲解析後的 JSON 數據，這提供了很大的靈活性，但可能不是最高效的方法。如果您知道 JSON 的確切結構，可以定義一個具體的 Rust 結構體來更高效地處理數據。

