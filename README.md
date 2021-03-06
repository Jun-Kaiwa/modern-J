# yarnの導入
yarnがインストールされているか
```
yarn -v
```
Linux環境を整えたばかりであればnpmのインストールなどをしてからyarnのインストールへ<br>
[npmインストール](https://docs.microsoft.com/ja-jp/windows/dev-environment/javascript/nodejs-on-wsl)

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

```diff
//src/pages/index.tsx

import Head from 'next/head'
+ import styles from '../styles/testpage.module.scss'

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

+        <p className={styles.scss_test}>
+          Scss Test
+        </p>

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
ブラウザで「Scss Test」と表示されれば設定完了
<br>

# Ant Designの導入　
[環境導入の参考記事](https://qiita.com/suin/items/29c8ccf0ddc73565c91d) <br>
[Ant Design](https://ant.design/)

<br>

パッケージのインストール
```
yarn add antd
```
<br>

管理画面レイアウトの作成（例）
```
//layouts/adminLayout.tsx

import {
  MenuFoldOutlined,
  MenuUnfoldOutlined,
  UploadOutlined,
  UserOutlined,
  VideoCameraOutlined,
} from "@ant-design/icons";
import { Layout, Menu } from "antd";
import { ReactNode, useState } from "react";
import styles from "./adminLayout.module.css";

const { Header, Sider, Content } = Layout;

function AdminLayout({ children }: { readonly children: ReactNode }) {
  const [collapsed, setCollapsed] = useState<boolean>(false);

  function toggle() {
    setCollapsed(!collapsed);
  }

  return (
    <Layout>
      <Sider trigger={null} collapsible collapsed={collapsed}>
        <div className={styles.logo} />
        <Menu theme="dark" mode="inline" defaultSelectedKeys={["1"]}>
          <Menu.Item key="1" icon={<UserOutlined />}>
            nav 1
          </Menu.Item>
          <Menu.Item key="2" icon={<VideoCameraOutlined />}>
            nav 2
          </Menu.Item>
          <Menu.Item key="3" icon={<UploadOutlined />}>
            nav 3
          </Menu.Item>
        </Menu>
      </Sider>
      <Layout className="site-layout">
        <Header className={styles.siteLayoutBackground} style={{ padding: 0 }}>
          {collapsed ? (
            <MenuUnfoldOutlined className={styles.trigger} onClick={toggle} />
          ) : (
            <MenuFoldOutlined className={styles.trigger} onClick={toggle} />
          )}
        </Header>
        <Content
          className={styles.siteLayoutBackground}
          style={{
            margin: "24px 16px",
            padding: 24,
            minHeight: 280,
          }}
        >
          {children}
        </Content>
      </Layout>
    </Layout>
  );
}

export default AdminLayout;
```
<br>

管理画面レイアウト用のCSSモジュールもlayouts/adminLayout.module.cssに作ります

```
//layouts/adminLayout.module.css

.trigger {
    padding: 0 24px;
    font-size: 18px;
    line-height: 64px;
    cursor: pointer;
    transition: color 0.3s;
}

.trigger:hover {
    color: #1890ff;
}

.logo {
    height: 32px;
    margin: 16px;
    background: rgba(255, 255, 255, 0.3);
}

.siteLayoutBackground {
    background: #fff;
}
```

<br>

Ant DesignのグローバルCSSをpages/_app.tsxにてimportします
```
//pages/_app.tsx

import 'antd/dist/antd.css';
```

<br>

pages/index.tsxがコード生成で作られているので、次のコードを追記
```diff
//pages/index.tsx

+ import dynamic from "next/dynamic"; 　　　　　　//import Head from 'next/head'より上の行でないとエラーになるようです

+ const AdminLayout = dynamic(() => import("../layouts/adminLayout"), {
+   ssr: false,
+ });

const Home: NextPage = () => {
  return (
    <div className={styles.container}>
      <Head>
        <title>Create Next App</title>
        <meta name="description" content="Generated by create next app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>

+     <AdminLayout>コンテンツ</AdminLayout>;
      
      <main className={styles.main}>
        <h1 className={styles.title}>
          Welcome to <a href="https://nextjs.org">Next.js!</a>
        </h1>
        .
        .
        (略)
        .
        .
  )
}

```

<br>

ブラウザに表示できていれば設定完了です
![スクリーンショット 2022-04-21 152714](https://user-images.githubusercontent.com/103023532/164388146-d5a3953b-1fcb-4ec5-a243-67c2bef17cb8.png)

<br>

# husky + lint-stagedの導入
パッケージのインストール
```
yarn add -D husky lint-staged
```

<br>

huskyの初期設定をする。
```
npx husky-init && yarn
```

<br>

最終的なpackage.jsonは以下のようになる。
```
// package.json

{
  "name": "next-ts-eslint-prettier-husky-lint-staged",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "prepare": "husky install"
  },
  "dependencies": {
    "next": "12.0.7",
    "react": "17.0.2",
    "react-dom": "17.0.2"
  },
  "devDependencies": {
    "@types/node": "17.0.0",
    "@types/react": "17.0.37",
    "@typescript-eslint/eslint-plugin": "^5.7.0",
    "@typescript-eslint/parser": "^5.7.0",
    "eslint": "8.4.1",
    "eslint-config-next": "12.0.7",
    "eslint-config-prettier": "^8.3.0",
    "husky": "^7.0.0",
    "lint-staged": "^12.1.2",
    "prettier": "^2.5.1",
    "typescript": "4.5.4"
  }
}
```

<br>

.husky/pre-commitを書き換える
```
//.husky/pre-commit

#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

yarn lint-staged
```

<br>

.eslintrc.jsonを下記のようにする
```
//.eslintrc.json

{
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint"],
  "extends": [
        "next/core-web-vitals",
        "plugin:import/recommended",
        "plugin:import/warnings",
        "prettier"
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

<br>

.lintstagedrc.jsを作成
```
// .lintstagedrc.js
module.exports = {
  '**/*.{js,jsx,ts,tsx}': [
    (filenames) =>
      `next lint --fix --file ${filenames
        .map((file) => file.split(process.cwd())[1])
        .join(' --file ')}`,
    'prettier --write',
  ],
}
```

## 動作の確認

index.tsxに下記コードを挿入する。
```
//src/pages/index.tsx

<a href="/">home</a>
```

<br>

git add、git commitをすると次のエラーが表示される。<br>
ESLint エラーとなり husky によってコミットが中断する。
```
Do not use the HTML <a> tag to navigate to /events/. Use Link from ‘next/link’ instead. <br>
See: https://nextjs.org/docs/messages/no-html-link-for-pages.eslint@next/next/no-html-link-for-pages
```

index.tsxをもとに戻して再度git add、git commitをしてコミットできることを確認する。
 
<br> 
 
# Jestの導入
## ライブラリのインストール
```
yarn add -D jest @testing-library/react @testing-library/jest-dom
```

<br>

## 設定ファイルの作成
```
//jest.config.js

const nextJest = require("next/jest");

const createJestConfig = nextJest({
  // next.config.jsとテスト環境用の.envファイルが配置されたディレクトリをセット。基本は"./"で良い。
  dir: "./",
});

// Jestのカスタム設定を設置する場所。従来のプロパティはここで定義。
const customJestConfig = {
  // jest.setup.jsを作成する場合のみ定義。
  // setupFilesAfterEnv: ["<rootDir>/jest.setup.js"],
  moduleNameMapper: {
    // aliasを定義 （tsconfig.jsonのcompilerOptions>pathsの定義に合わせる）
    "^@/components/(.*)$": "<rootDir>/components/$1",
    "^@/pages/(.*)$": "<rootDir>/pages/$1",
  },
  testEnvironment: "jest-environment-jsdom",
};

// createJestConfigを定義することによって、本ファイルで定義された設定がNext.jsの設定に反映されます
module.exports = createJestConfig(customJestConfig);

```

## テストを書いて動かしてみる
```
//src/components/Sample.tsx

export const Sample = () => {
  return (
    <main>
      <h1>Nextjs+Jestのサンプルサプリ</h1>
      <p>設定がすごく楽になりました。</p>
    </main>
  );
};
```

<br>

```
//src/components/__tests__/Sample.spec.tsx

import { Sample } from "@/components/Sample";
import { render } from "@testing-library/react";

describe("Sampleコンポーネント", () => {
  test("should first", () => {
    const { getByText } = render(<Sample />);
    expect(getByText("Nextjs+Jestのサンプルサプリ")).toBeTruthy();
    expect(getByText("設定がすごく楽になりました。")).toBeTruthy();
  });
});
```

<br>

```diff
//package.json

{
 "scripts": {
   "dev": "next dev",
   "build": "next build",
   "start": "next start",
+   "test": "jest --watch",
   "lint": "next lint"
 },
}
```

<br>

```
yarn test
```
テストによってエラーが出る。
下記のように直すとテストが通ります。
```diff
//src/components/__tests__/Sample.spec.tsx

- import { Sample } from "@/components/Sample";
+ import { Sample } from "../Sample";
import { render } from "@testing-library/react";

describe("Sampleコンポーネント", () => {
  test("should first", () => {
    const { getByText } = render(<Sample />);
    expect(getByText("Nextjs+Jestのサンプルサプリ")).toBeTruthy();
    expect(getByText("設定がすごく楽になりました。")).toBeTruthy();
  });
});
```
