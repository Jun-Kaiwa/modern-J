# Node.js (TypeScript)
## Node.jsをインストール
[公式サイト](https://nodejs.org/ja/)

yarnの初期化をする。
```
yarn init
```
これによって、package.jsonが作成される。

<br>

## パッケージのインストール
```
yarn add -D typescript ts-node @types/node
```

tsconfig.jsonを作成
```
{
  "compilerOptions": {
    "target": "es2020",
    "module": "commonjs",
    "lib": [
      "es2020"
    ],
    "sourceMap": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "moduleResolution": "node",
    "baseUrl": "src",
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "dist",
    "node_modules"
  ],
  "compileOnSave": false
}
```

実行コマンドの追加
```
//package.json

 "scripts": {
    "start": "node dist/index.js",
    "build": "tsc",
    "dev": "ts-node src/index.ts"
  },
```

<br>

動作確認
サンプルファイルを作成する
```
//src/index.ts

const message: string = "Hello, world!";
console.log(message)
```

```
yarn dev

yarn run v1.22.18
$ ts-node src/index.ts
Hello, world!
Done in 0.76s.
```
<br>

# ESLint + Prettierの導入
[ESLint + Prettierを導入したTypeScript開発環境](https://zenn.dev/big_tanukiudon/articles/c1ab3dba7ba111)

# ｘhusky + lint-stagedの導入
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
