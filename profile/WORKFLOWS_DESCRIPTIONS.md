## Описание глобальных Workflows

### Frontend Build ([frontend_build.yml](../.github/workflows/frontend_build.yml))

- Автоматическая сборка фронтенд-проекта.
- Выполняет клонирование репозитория, установку зависимостей, сборку проекта для текущей ветки и деплой в отдельную ветку (`build_<branch>`).
- Выбор `npm`, `yarn`, `bun`, по умолчанию `npm`.
- Поддерживает автоматическое обновление версии (major, minor, patch) и работу с приватными пакетами GitHub Packages.

- Пример использования:

```yml
name: Frontend Build Wrapper

on:
  workflow_dispatch:
    inputs:
      version_type:
        description: 'Version bump'
        required: false
        default: 'none'
        type: choice
        options:
          - none
          - major
          - minor
          - patch
      package_manager:
        description: 'Package manager'
        required: false
        default: 'bun'
        type: choice
        options:
          - npm
          - yarn
          - bun
      add_branch_name:
        description: 'Add branch name to version'
        required: false
        type: boolean
        default: true
      add_build_number:
        description: 'Add build number to version'
        required: false
        type: boolean
        default: true

jobs:
  alpha:
    if: github.ref_name == 'alpha'
    uses: seven-winds-studio/.github/.github/workflows/frontend_build.yml@main
    with:
      version_type: ${{ inputs.version_type }}
      package_manager: ${{ inputs.package_manager }}
      array_config_mode: ${{ vars.CONFIG_DEV }}
      add_branch_name: ${{ inputs.add_branch_name }}
      add_build_number: ${{ inputs.add_build_number }}

    secrets:
      SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY_DEV }}
      ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  prod:
    if: github.ref_name == 'prod'
    uses: seven-winds-studio/.github/.github/workflows/frontend_build_old.yml@main
    with:
      version_type: ${{ inputs.version_type }}
      package_manager: ${{ inputs.package_manager }}
    secrets:
      ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  demo:
    if: github.ref_name == 'demo'
    uses: seven-winds-studio/.github/.github/workflows/frontend_build.yml@main
    with:
      version_type: ${{ inputs.version_type }}
      package_manager: ${{ inputs.package_manager }}
      array_config_mode: ${{ vars.CONFIG_DEMO }}
      add_branch_name: ${{ inputs.add_branch_name }}
      add_build_number: ${{ inputs.add_build_number }}

    secrets:
      SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY_PROD }}
      ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

### Library Publish ([library_publish.yml](../.github/workflows/library_publish.yml))

- Публикация npm-библиотеки в выбранный реестр: GitHub Packages, npm или оба сразу.
- Выполняет автоматический bump версии, пушит теги и коммиты, проверяет наличие версии в реестрах и публикует пакет только если версия ещё не опубликована.
- Поддерживает работу с приватными пакетами и опциональный токен для публикации в npm.
- Выбор `npm`, `yarn`, `bun`, по умолчанию `npm`.
- Пример использования:

  ```yml
  name: Library Publish Wrapper

  on:
    workflow_dispatch:
      inputs:
        registry_type:
          description: "Registry type"
          required: true
          default: "npm"
          type: choice
          options:
            - npm
            - github
            - all
        version_type:
          description: "Version bump"
          required: true
          default: "patch"
          type: choice
          options:
            - major
            - minor
            - patch
        package_manager:
          description: "Package manager"
          required: false
          default: "npm"
          type: choice
          options:
            - npm
            - yarn
            - bun

  jobs:
    call-publisher:
      uses: seven-winds-studio/.github/.github/workflows/library_publish.yml@main
      with:
        registry_type: ${{ inputs.registry_type }}
        version_type: ${{ inputs.version_type }}
        package_manager: ${{ inputs.package_manager }}
      secrets:
        NPM_TOKEN: ${{ secrets.NPM_ORGANIZATION_TOKEN }}
        ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN  }}
  ```

---

### Library Storybook ([library_storybook.yml](../.github/workflows/library_storybook.yml))

- Сборка и деплой Storybook для библиотеки.
- Выполняет установку зависимостей, сборку Storybook и деплой собранной документации в отдельную ветку (`build_storybook`).
- Использует приватные пакеты из GitHub Packages.
- Выбор `npm`, `yarn`, `bun`, по умолчанию `npm`.
- Пример использования:

  ```yml
  name: Library Storybook Wrapper

  on:
    workflow_dispatch:
      inputs:
        package_manager:
          description: "Package manager"
          required: false
          default: "npm"
          type: choice
          options:
            - npm
            - yarn
            - bun
  jobs:
    call-publisher:
      uses: seven-winds-studio/.github/.github/workflows/library_storybook.yml@main
      with:
        package_manager: ${{ inputs.package_manager }}
      secrets:
        ACCESS_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN  }}
  ```

### Frontend README VERSIONS ([frontend_readme_versions.yml](../.github/workflows/frontend_readme_versions.yml))
- Запускается после успешного завершения указанного workflow.
- Клонирует основную ветку репозитория и подтягивает все ветки.
- Для каждой ветки, соответствующей шаблону `build_*`, определяет имя целевой ветки и версию из её `package.json`.
- Формирует блок `## 🚀 ACTUAL VERSIONS` с перечислением всех веток и их версий.
- Обновляет или добавляет этот блок в `README.md` (заменяет старый или добавляет новый).
- Коммитит и пушит изменения в основную ветку, если были изменения.
- Пример использования:

  ```yml
  name: Frontend README Versions Wrapper

  on:
    workflow_dispatch:
    workflow_run:
      workflows: ['Frontend Build Wrapper']
      types:
        - completed

  jobs:
    call-global-workflow:
      uses: seven-winds-studio/.github/.github/workflows/frontend_readme_versions.yml@main
  ```