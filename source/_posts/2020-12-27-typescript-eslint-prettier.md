---
title: typescript-eslint-prettier
date: 2020-12-27 19:52:24
tags:
categories:
---
# 기본 설정

## eslint 설치

아래 패키지들을 설치한다.

```bash
$ yarn add -D eslint typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin
$ npm install --save-dev eslint typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

- `eslint`: Javascript Linter
- `typescript`: Typescript... 설치 안 했다면 하자 ^-^
- `@typescript-eslint/parser`: 타입스크립트에서 ESLint를 사용할 수 있게 하는 파서
- `@typescript-eslint/eslint-plugin`: 코드베이스에서 ESLint 규칙을 적용하는 플러그인

## 설정

프로젝트 루트에 `.eslintrc.js` 파일을 생성한 다음 아래 코드를 복붙한다.

```javascript
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: [
    '@typescript-eslint',
  ],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
  ],
};
```

- `root`: true로 설정하지 않으면 파일 시스템의 상위 폴더까지 탐색하기 때문에 설정해준다.
- `parser`: 위에서 설치한 parser를 적용한다.
- `plugin`: 위에서 설치한 plugin을 적용한다.
- `extends`
    - `eslint:recommended`을 적용하면 ESLint의 내장되어있는 추천 설정을 사용할 수 있다.
    - `@typescript-eslint/recommended`는 `eslint:recommended` 와 비슷하다. 이걸 적용하면 타입스크립트 전용 규칙을 추가적으로 사용할 수 있다.

### env

여기에 추가적으로 `env`를 설정한다. 미리 정의된 전역 변수를 정의해준다.
브라우저 환경에서 lint를 돌릴 경우 `env`에 `browser`를 추가해준다.

```javascript
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: [
    '@typescript-eslint',
  ],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
  ],
  env: {
    browser: true,
  }
};
```

## 불필요한 파일 무시

`.eslintignore` 파일 만들고 무시할 파일/폴더를 추가해준다.

```
node_modules
dist
```

## 실행

```bash
$ yarn eslint . --ext .js,.jsx,.ts,.tsx
$ npx eslint . --ext .js,.jsx,.ts,.tsx
```

여기서 `--ext`는 javascript file extensions을 설정하는 옵션이다. 기본값은 `.js`이다.

# Airbnb/Standard 규칙 적용하기

- Airbnb's ESLint config - [eslint-config-airbnb-typescript](https://www.npmjs.com/package/eslint-config-airbnb-typescript).
- Standard - [eslint-config-standard-with-typescript](https://www.npmjs.com/package/eslint-config-standard-with-typescript).

둘 중 원하는 거 설치해서 적용하면 된다. 나는 Standard 설정을 적용해보았다.
extends의 eslint 추천 설정들을 삭제하고 원하는 스타일을 적용하면 된다.

```javascript
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: [
    '@typescript-eslint',
  ],
  extends: ['standard-with-typescript'],
  parserOptions: {
    project: './tsconfig.js'
  },
	ignorePatterns: ['.eslintrc.js'],
};
```

이때 `parserOptions.project`을 설정해줘야 된다. 없으면 에러가 발생한다.
왜 에러가 발생하나 했더니... 타입 명시가 필요한 규칙의 경우 이 설정이 필요하다고 나와있다. ([문서](https://github.com/typescript-eslint/typescript-eslint/blob/master/packages/parser/README.md#parseroptionsproject) 참고)
`parserOptions.project` 을 설정하면 ESLint가 eslintrc.js 파일도 lint를 시도하기 때문에 `ignorePatterns`을 추가한다.

# Prettier와 함께 사용하기

## 의존성 설치

```bash
$ yarn add -D  --save-dev prettier eslint-plugin-prettier eslint-config-prettier
$ npm install --save-dev prettier eslint-plugin-prettier eslint-config-prettier
```

- `prettier`: prettier
- `eslint-config-prettier`: 불필요하거나 Prettier와 충돌할 수 있는 모든 규칙을 비활성화
- `eslint-plugin-prettier`: 코드 포매팅할 때 Prettier를 사용하게 만드는 규칙을 추가

## .eslintrc에 prettier 설정 추가

```javascript
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint', 'prettier'],
  extends: ['standard-with-typescript', 'prettier', 'plugin:prettier/recommended'],
  parserOptions: {
    project: './tsconfig.json',
  },
  rules: {
    '@typescript-eslint/semi': 'off',
    '@typescript-eslint/space-before-function-paren': 'off',
  },
};
```

`eslint-config-prettier` 추가한다고 해도 충돌이 나는 경우가 있는데, 이 경우 rules 옵션 설정해주면 된다.

## .prettierrc 생성

```javascript
{
  "trailingComma": "all",
  "tabWidth": 2,
  "semi": true,
  "singleQuote": true,
  "printWidth": 160,
  "parser": "typescript"
}
```

# 참고 문서

[typescript-eslint/typescript-eslint](https://github.com/typescript-eslint/typescript-eslint/blob/master/docs/getting-started/linting/README.md#configuration)