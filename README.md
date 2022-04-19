# yarnの導入
yarnがインストールされているか
```
yarn -v
```
<br>

yarnのインストール
```
npm install -g yarn
```
<br>

# アプリケーションの作成（Next.js with TypeScript）
sample-appをアプリ名にして
```
npx create-next-app sample-app --typescript
```
<br>

ディレクトリ移動
```
cd sample-app
```
<br>
  
ビルドコマンドを実行
```
yarn dev
```
<br>
  
path aliasの設定(モジュールの import で相対パスしか使えないと辛いので、パスエイリアスを設定しておきます。)
```diff
{
 "compilerOptions": {
   "target": "es5",
   "lib": ["dom", "dom.iterable", "esnext"],
   "allowJs": true,
   "skipLibCheck": true,
   "strict": true,
   "forceConsistentCasingInFileNames": true,
   "noEmit": true,
   "esModuleInterop": true,
   "module": "esnext",
   "moduleResolution": "node",
   "resolveJsonModule": true,
   "isolatedModules": true,
   "jsx": "preserve",
- "incremental": true
+ "incremental": true,
+ "baseUrl": "./",
+ "paths": {
+  "@/*": ["./src/*"]
+ }
 },
 "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
 "exclude": ["node_modules"]
}
```
```
// 相対パスだけだと辿るのが辛い...
import moduleA from '../../../../modules/A'

// 絶対パスなら可読性も上がる！
import moduleA from '@/modules/A'
```
<br>

srcディレクトリを作成してpagesとstylesを入れる（ソースコードの見通し）
```
.
├── public
│   ├── favicon.ico
│   └── vercel.svg
├── src
│   ├── pages
│   │   ├── api
│   │   │   └── hello.ts
│   │   ├── _app.tsx
│   │   └── index.tsx
│   └── styles
│       ├── Home.module.css
│       └── globals.css
├── README.md
├── next-env.d.ts
├── next.config.js
├── package.json
├── tsconfig.json
└── yarn.lock
```

# ESLintの導入
ESLint は Next.js v11.0.0 以降では標準実装されています。
以下を実行して動作することを確認します。
<br>
ライブラリのインストール
```
yarn add -D eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser
```
<br>

.eslintrc.jsonに追加設定したい項目を追記
```diff
{
-  "extends": "next/core-web-vitals"
+  "extends": "next/core-web-vitals"
+  "extends": [
+    "next/core-web-vitals",
+    "plugin:import/recommended",
+    "plugin:import/warnings"
+  ],
+  "rules": {
+    "import/order": [
+      "error",
+      {
+        "alphabetize": {
+          "order": "asc"
+        }
+      }
+    ]
+  }
}
```
<br>

# Prettierの導入
ライブラリのインストール
```
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
```
<br>

ESLint と Prettier が干渉しないように、ESLint 側に設定を追加します。
```diff
//.eslintrc.json
{
 "extends": [
   "next/core-web-vitals",
   "plugin:import/recommended",
-    "plugin:import/warnings"
+    "plugin:import/warnings",
+    "prettier"
 ],
 "rules": {
   "import/order": [
     "error",
     {
       "alphabetize": {
         "order": "asc"
       }
     }
   ]
 }
}
```

.prettierrcファイルの追加 もしくはpackage.jsonにルールを記述
```
//.prettierrc
{
    "trailingComma": "all",// 末尾のカンマあり
    "tabWidth": 2,// tab の長さは半角スペース 2 つ
    "semi": true,// セミコロンあり
    "singleQuote": true,// シングルクォーテーションに統一
    "jsxSingleQuote": true,//jsx もシングルクォーテーションに統一
    "printWidth": 100 // １ 行の最大文字数 100
  },
```
```
// package.json
  "prettier": {
    "trailingComma": "all",// 末尾のカンマあり
    "tabWidth": 2,// tab の長さは半角スペース 2 つ
    "semi": true,// セミコロンあり
    "singleQuote": true,// シングルクォーテーションに統一
    "jsxSingleQuote": true,//jsx もシングルクォーテーションに統一
    "printWidth": 100 // １ 行の最大文字数 100
  },
```
<br>

# CSS Moduleの導入
Next.js で標準サポートされている
