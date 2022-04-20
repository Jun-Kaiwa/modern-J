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
```diff JSON:aaa.json
//tsconfig.json
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
    "editor.formatOnSave": true, //保存時にフォーマット
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
    "editor.formatOnSave": true, //保存時にフォーマット
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
Next.js で標準サポートされている。<br>
stylesディレクトリ内を下記のように分ける
<br>
```
styles/global.scss
```
- ここにグローバルに（アプリ全体で）読み込みたいスタイルを書く。
- _app.tsxでimport styles/global.scssするだけで読み込み設定は完了。

<br>

```
styles/components
```
- コンポーネント用の.module.scssを入れてく


<br>

# Sass(Scss)の導入
sassのインストール
```
yarn add sass
```
scssの使用例（css moduleなので〇〇.module.scssでファイルを作成する）
```
//src/styles/testpage.module.scss
p {
  &.scss_test {
    font-size: 3em;
  }
}
```

```
//src/pages/index.tsx
import Head from 'next/head'
import styles from '../styles/testpage.module.scss'

export default function Home() {
  return (
    <div className="container">
      <Head>
        <title>Create Next App</title>
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <main>
        <h1 className="title">
          Welcome to <a href="https://nextjs.org">Next.js!</a>
        </h1>

        <p className={styles.scss_test}>
          Scss Test
        </p>

        <p className="description">
          Get started by editing <code>pages/testpage.tsx</code>
        </p>
{/* 〜中略〜 */}
      </main>
{/* 〜中略〜 */}
    </div>
  )
}
```
<br>

