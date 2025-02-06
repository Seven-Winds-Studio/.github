## Конфигурация ESLint для React

`package.json`

```json
"devDependencies": {
    "@eslint/js": "^9.17.0",
    "eslint": "^9.17.0",
    "eslint-config-prettier": "^9.1.0",
    "eslint-plugin-prettier": "^5.2.1",
    "eslint-plugin-react": "^7.37.3",
    "eslint-plugin-react-hooks": "^5.0.0",
    "eslint-plugin-react-refresh": "^0.4.16",
    "globals": "^15.14.0",
    "typescript-eslint": "^8.18.2",
}
```

`eslint.config.mjs`

```js
import pluginJs from "@eslint/js";
import configPrettier from "eslint-config-prettier";
import pluginPrettier from "eslint-plugin-prettier";
import pluginReact from "eslint-plugin-react";
import pluginReactHooks from "eslint-plugin-react-hooks";
import globals from "globals";
import pluginTypescript from "typescript-eslint";

export default [
  { files: ['**/*.{js,mjs,cjs,ts,jsx,tsx}'] },
  {
    languageOptions: {
      parser: pluginTypescript.parser,
      globals: {
        ...globals.node,
        ...globals.browser
      }
    }
  },
  ...pluginTypescript.configs.recommended,
  pluginReact.configs.flat.recommended,
  pluginJs.configs.recommended,
  configPrettier,
  {
    plugins: {
      "react-hooks": pluginReactHooks,
      prettier: pluginPrettier,
    },
    rules: {
      "react-hooks/rules-of-hooks": "error",
      "react-hooks/exhaustive-deps": "warn",
      "prettier/prettier": "error",
      "react/react-in-jsx-scope": "off",
      "no-unused-vars": "off",
      "@typescript-eslint/no-unused-vars": "warn",
    },
    settings: {
      react: {
        version: "detect",
      },
    },
  },
];
```
