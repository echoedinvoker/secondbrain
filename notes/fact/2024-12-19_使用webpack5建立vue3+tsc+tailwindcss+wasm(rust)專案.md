---
date: 2024-12-19
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# 使用webpack5建立vue3+tsc+tailwindcss+wasm(rust)專案

💬:

1. 創建專案目錄：

```bash
mkdir vue3-webpack5-wasm-project
cd vue3-webpack5-wasm-project
```

2. 初始化 npm 專案：

```bash
npm init -y
```

3. 安裝必要的依賴：

```bash
npm install vue@3 typescript webpack@5 webpack-cli webpack-dev-server vue-loader@next @vue/compiler-sfc css-loader style-loader ts-loader html-webpack-plugin mini-css-extract-plugin tailwindcss postcss postcss-loader autoprefixer
```

4. 安裝開發依賴：

```bash
npm install -D @types/node @types/webpack-env
```

5. 創建 TypeScript 配置文件 `tsconfig.json`：

```json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "strict": true,
    "jsx": "preserve",
    "moduleResolution": "node",
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "useDefineForClassFields": true,
    "sourceMap": true,
    "baseUrl": ".",
    "types": ["webpack-env"],
    "paths": {
      "@/*": ["src/*"]
    },
    "lib": ["esnext", "dom", "dom.iterable", "scripthost"]
  },
  "include": ["src/**/*.ts", "src/**/*.tsx", "src/**/*.vue", "src/**/*.d.ts"],
  "exclude": ["node_modules"]
}
```

6. 創建 Webpack 配置文件 `webpack.config.js`：

```javascript
const path = require('path');
const { VueLoaderPlugin } = require('vue-loader');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  mode: 'development',
  entry: './src/main.ts',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      },
      {
        test: /\.ts$/,
        loader: 'ts-loader',
        options: {
          appendTsSuffixTo: [/\.vue$/],
          transpileOnly: true  // 加速 tsc compiler, 並減少 tsc 的錯誤
        },
      },
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader',
        ],
      },
    ],
  },
  resolve: {
    extensions: ['.ts', '.js', '.vue', '.json', '.wasm'],
    alias: {
      '@': path.resolve(__dirname, 'src'),
    },
  },
  plugins: [
    new VueLoaderPlugin(),
    new HtmlWebpackPlugin({
      template: './public/index.html',
    }),
    new MiniCssExtractPlugin({
      filename: 'styles.css',
    }),
  ],
  devServer: {
    static: {
      directory: path.join(__dirname, 'public'),
    },
    compress: true,
    port: 9000,
  },
};
```

7. 創建 `public/index.html`：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vue 3 + Webpack 5 + TypeScript + Tailwind + WASM</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

8. 創建 `src/main.ts`：

```typescript
import { createApp } from 'vue'
import App from './App.vue'
import './styles/main.css'

createApp(App).mount('#app')
```

9. 創建 `src/App.vue`：

```vue
<template>
  <div class="container mx-auto mt-10">
    <h1 class="text-4xl font-bold text-center text-blue-600">{{ message }}</h1>
    <p class="text-center mt-4">{{ wasmResult }}</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue'
import init, { greet } from '@/wasm/rust_lib'

export default defineComponent({
  name: 'App',
  setup() {
    const message = ref('Hello Vue 3 + Webpack 5 + TypeScript + Tailwind + WASM!')
    const wasmResult = ref('')

    onMounted(async () => {
      await init()
      wasmResult.value = greet('WebAssembly')
    })

    return {
      message,
      wasmResult
    }
  }
})
</script>
```

10. 創建 `src/styles/main.css`：

```css
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';
```

11. 創建 Tailwind 配置文件 `tailwind.config.js`：

```javascript
module.exports = {
  content: [
    './src/**/*.{vue,js,ts,jsx,tsx}',
    './public/index.html',
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

12. 創建 PostCSS 配置文件 `postcss.config.js`：

```javascript
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

13. 在 src 目錄下創建一個 shims-vue.d.ts 文件：

```bash
touch src/shims-vue.d.ts
```

然後在這個文件中添加以下內容：

```typescript
declare module '*.vue' {
  import type { DefineComponent } from 'vue'
  const component: DefineComponent<{}, {}, any>
  export default component
}
```
14. 在專案根目錄創建 Rust 庫：

```bash
cargo new --lib rust_lib
cd rust_lib
```

15. 編輯 `rust_lib/Cargo.toml`：

```toml
[package]
name = "rust_lib"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
wasm-bindgen = "0.2"
```

16. 編輯 `rust_lib/src/lib.rs`：

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn greet(name: &str) -> String {
    format!("Hello, {}! This message is from Rust!", name)
}
```

17. 在 `rust_lib` 目錄中構建 WebAssembly：

```bash
wasm-pack build --target web
```

18. 將生成的 WebAssembly 模組移動到 Vue 專案中：

```bash
mkdir -p ../src/wasm
cp -r pkg/* ../src/wasm/
cd ..
```

19. 更新 `package.json` 中的 scripts：

```json
"scripts": {
  "dev": "webpack serve",
  "build": "webpack --mode production",
  "type-check": "tsc --noEmit"
}
```

19. 運行開發伺服器：

```bash
npm run dev
```

現在，您應該有一個完整的 Vue 3 + TypeScript + Tailwind CSS + WebAssembly (Rust) 專案，使用 Webpack 5 進行構建。您可以訪問 `http://localhost:9000` 來查看您的應用程序。

如果您需要構建生產版本，只需運行：

```bash
npm run build
```

這將在 `dist` 目錄中生成生產就緒的文件。

