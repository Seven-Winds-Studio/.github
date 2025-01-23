## Конфигурация Prettier для React

`package.json`

```json
"devDependencies": {
  "@trivago/prettier-plugin-sort-imports": "^5.2.0",
  "prettier": "^3.4.2"
}
```

`prettier.config.mjs`

```js
export default {
  printWidth: 120,
  tabWidth: 2,
  semi: false,
  singleQuote: true,
  trailingComma: "none",
  bracketSpacing: true,
  arrowParens: "avoid",
  endOfLine: "auto",
  plugins: ["@trivago/prettier-plugin-sort-imports"],
  importOrder: ["<THIRD_PARTY_MODULES>", "^@local/(.*)$", "^[./]", ".css"],
  importOrderSeparation: true,
  importOrderSortSpecifiers: true,
};
```
