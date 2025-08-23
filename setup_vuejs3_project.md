# Настройка проекта Vue.js 3

Эта статья рассказывает как настроить новый проект на Vue.js 3 без использования TypeScript.

Цель - единообразие и нежелание тратить время на штуки, которые могут быть автоматизированы - порядок импортов, атрибутов, CSS правил.

За основу взят официальный стайлгайд - <https://ru.vuejs.org/style-guide/>

Используются:

- EditorConfig
- Prettier
- ESLint
- StyleLint

Порядок выполнения (по идее должен быть такой):

EditorConfig → Prettier (format) → ESLint (lint/fix для JS/TS) и Stylelint (lint/fix для стилей).

Если Prettier встроен в ESLint (plugin), тогда Prettier фактически выполняется как часть ESLint.

## Установка зависимостей

```sh
npm install -D \
  eslint \
  eslint-plugin-vue \
  vue-eslint-parser \
  eslint-plugin-import \
  eslint-config-prettier \
  prettier \
  stylelint \
  stylelint-order \
  stylelint-scss \
  stylelint-config-recommended \
  stylelint-config-standard \
  stylelint-config-recess-order

```

## EditorConfig

```.editorconfig
# EditorConfig is awesome: https://EditorConfig.org

root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.py]
indent_size = 4

[*.md]
trim_trailing_whitespace = false

[Makefile]
indent_style = tab
```

## Prettier

.prettierrc.json

```json
{
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "arrowParens": "always",
  "endOfLine": "lf",
  "vueIndentScriptAndStyle": true
}
```

## ESLint

ESLint конфигурация, ориентированная на Vue 3, Composition API, TypeScript (опционально) и интеграцию с Prettier.

Пример для JavaScript (без TypeScript)

.eslint.config.js

```js
import fs from "fs";
import path from "path";

export default {
  root: true,
  env: {
    browser: true,
    node: true,
    es2021: true,
  },
  // parserOptions default works for modern JS, no babel/ts
  parserOptions: {
    ecmaVersion: 2021,
    sourceType: "module",
  },
  plugins: ["vue", "import", "prettier"],
  extends: [
    "eslint:recommended",
    "plugin:vue/vue3-recommended",
    "plugin:import/recommended",
    "plugin:import/errors",
    "plugin:import/warnings",
    "prettier",
  ],
  rules: {
    // Prettier integration
    "prettier/prettier": ["error"],

    // Vue specifics
    "vue/html-indent": ["error", 2],
    "vue/max-attributes-per-line": [
      "error",
      {
        singleline: 3,
        multiline: {
          max: 1,
          allowFirstLine: false,
        },
      },
    ],
    "vue/singleline-html-element-content-newline": "off",
    "vue/multiline-html-element-content-newline": "off",
    "vue/html-self-closing": [
      "error",
      {
        html: {
          void: "never",
          normal: "always",
          component: "always",
        },
        svg: "always",
        math: "always",
      },
    ],
    "vue/attributes-order": [
      "error",
      {
        order: [
          "DEFINITION",
          "LIST_RENDERING",
          "CONDITIONALS",
          "RENDER_MODIFIERS",
          "GLOBAL",
          "UNIQUE",
          "BINDING",
          "OTHER_ATTR",
          "EVENTS",
          "CONTENT",
        ],
        alphabetical: false,
      },
    ],

    // Import order (grouping and alphabetize within groups)
    "import/order": [
      "error",
      {
        groups: [
          "builtin",
          "external",
          "internal",
          ["parent", "sibling", "index"],
          "object",
          "type",
        ],
        pathGroups: [
          {
            pattern: "vue",
            group: "external",
            position: "before",
          },
          {
            pattern: "@/**",
            group: "internal",
          },
        ],
        pathGroupsExcludedImportTypes: ["builtin"],
        "newlines-between": "always",
        alphabetize: {
          order: "asc",
          caseInsensitive: true,
        },
      },
    ],

    // General JS rules
    "no-unused-vars": [
      "warn",
      { argsIgnorePattern: "^_", varsIgnorePattern: "^_" },
    ],
    "no-console": ["warn", { allow: ["warn", "error"] }],
    "prefer-const": ["error"],
    "no-var": ["error"],
    eqeqeq: ["error", "always"],
    curly: ["error", "all"],
    "arrow-spacing": ["error", { before: true, after: true }],
  },
  settings: {
    "import/resolver": {
      node: {
        extensions: [".js", ".vue"],
        moduleDirectory: ["node_modules", "src"],
      },
    },
  },
  overrides: [
    {
      files: ["*.vue"],
      parser: "vue-eslint-parser",
      parserOptions: {
        parser: "espree",
        ecmaVersion: 2021,
        sourceType: "module",
      },
    },
    {
      files: ["*.test.js", "**/__tests__/**"],
      env: { jest: true },
    },
  ],
};
```

## StyleLint

stylelint.config.js

```js
module.exports = {
  root: true,
  plugins: ["stylelint-order", "stylelint-scss"],
  extends: [
    "stylelint-config-recommended",
    "stylelint-config-standard",
    "stylelint-config-recess-order", // sensible defaults for order (we override certain rules below)
  ],
  rules: {
    // Basic
    "block-no-empty": true,
    "color-no-invalid-hex": true,
    "declaration-block-trailing-semicolon": "always",
    "no-descending-specificity": null,

    // Order of content inside stylesheet
    "order/order": [
      "custom-properties",
      "dollar-variables",
      "at-rules",
      "declarations",
      {
        type: "at-rule",
        name: "include",
      },
      "rules",
    ],
    // inside rules: position of things (for BEM / conventional ordering)
    "order/properties-order": [
      // grouping example (feel free to extend)
      {
        groupName: "position",
        properties: ["position", "top", "right", "bottom", "left", "z-index"],
      },
      {
        groupName: "box",
        properties: [
          "display",
          "flex",
          "flex-direction",
          "flex-wrap",
          "justify-content",
          "align-items",
          "gap",
          "align-content",
        ],
      },
      {
        groupName: "box-model",
        properties: [
          "box-sizing",
          "width",
          "min-width",
          "max-width",
          "height",
          "min-height",
          "max-height",
          "margin",
          "padding",
        ],
      },
      {
        groupName: "typography",
        properties: [
          "font-family",
          "font-size",
          "font-weight",
          "line-height",
          "letter-spacing",
          "text-align",
          "color",
        ],
      },
      {
        groupName: "visual",
        properties: [
          "background",
          "background-color",
          "border",
          "border-radius",
          "box-shadow",
          "opacity",
          "overflow",
        ],
      },
    ],

    // SCSS plugin rules (safe to keep)
    "scss/at-rule-no-unknown": true,
  },
  ignoreFiles: [
    "**/*.js",
    "**/*.jsx",
    "**/*.ts",
    "**/*.tsx",
    "**/node_modules/**",
  ],
};
```

## .prettierignore и .eslintignore (рекомендация)

```
node_modules
dist
public
coverage
```

## Настройка VSCode

.vscode/settings.json

```json
{
  // Форматирование
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.organizeImports": true
  },

  // ESLint
  "eslint.enable": true,
  "eslint.validate": ["javascript", "javascriptreact", "vue"],
  "eslint.packageManager": "npm",
  "eslint.run": "onSave",
  "eslint.alwaysShowStatus": true,

  // Prettier
  "prettier.requireConfig": true,
  "prettier.enableDebugLogs": false,

  // Stylelint
  "stylelint.enable": true,
  "stylelint.validate": ["css", "scss", "vue"],
  "stylelint.packageManager": "npm",

  // Vue Volar / Vetur: use Volar for Vue 3 (no TS)
  // If you prefer Vetur instead of Volar, set the appropriate extensions and disable Volar.
  "volar.codeLens.references": false,
  "volar.codeLens.scriptSetupTools": false,

  // Files to exclude from search/processing
  "files.exclude": {
    "**/.git": true,
    "**/node_modules": true,
    "**/.next": true,
    "**/dist": true
  },

  // Organize imports behavior for JavaScript
  "javascript.preferences.importModuleSpecifier": "non-relative",

  // Optional: limit Prettier line length consistent with .prettierrc
  "prettier.printWidth": 100
}
```

.vscode/extensions.json

```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "stylelint.vscode-stylelint",
    "vue.volar"
  ]
}
```
