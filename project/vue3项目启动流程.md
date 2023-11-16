## vue3项目启动

```bash
yarn create vite my-vue-app --template vue-ts
```

之后再进入项目文件夹安装依赖即可。**下面的各种流程依赖包按需安装。**



## 代码校验与风格

使用ESLint做JS、TS代码风格检测和限制，使用Prettier控制代码风格。进行团队风格统一化。

### 1. 配置ESlint

首先安装一下vscode的ESLint插件，然后安装eslint包：

```bash
# 下载
yarn add eslint -D
```

ESLint 安装成功后，执行 `npx eslint --init`，在安装完一个配置包后，此时进入到配置项选择：

**希望如何使用ESLint** 选第二个：*检查语法，发现问题*

**选择什么模块风格** 选第一个：*ES6的模块化*

**所使用的项目** 按实选择即可

**是否使用了TS** 按实选择即可

**项目在什么环境运行** 按实选择即可，可多选

**希望以什么格式的文件保存配置** 选JS即可

**是否现在就进行安装** yes

**使用什么包管理器安装** 与项目保存统一选择即可



操作完成后，会在项目根目录下会生成`.eslintrc.cjs` 配置文件。但是我们发现似乎依然有一些问题：

1. 在`.eslintrc.cjs` 配置文件中的 `module.export` 报错了，这是因为可能我们在刚才配置项项目环境选择时只选择了browser，导致eslint对modul报错。**在此文件下的 env 配置项里添加 "node": true 即可解决。**

2. 我们也发现在vue文件的头部eslint对 `>` 报错，这是默认配置项错误导致，**我们只需将在`.eslintrc.cjs` 配置文件下的 parser 配置项修改为 "vue-eslint-parser"，然后将原来的配置添加到 parserOptions 配置项即可。**

   ```json
   "parser": "vue-eslint-parser",
   "parserOptions": {
       "ecmaVersion": "latest",
       "parser": "@typescript-eslint/parser",
       "sourceType": "module"
   },
   ```

3. 接着在 `package.json` 中的 script 项添加修复指令，这样就可以运行指令修复：

   ```json
   "scripts": {
     "dev": "vite",
     "build": "vue-tsc && vite build",
     "preview": "vite preview",
     "lint:eslint": "eslint . --ext .vue,.js,.ts,.jsx,.tsx --fix"
   },
   ```

4. 如果我们想在保存时自动修复eslint的错误，可以在settings.json中添加如下配置(需要eslint插件):

   ```json
   "editor.codeActionsOnSave": {
       "source.fixAll.eslint": true
   },
   ```

最后在项目根目录下添加 `.eslintignore` 配置eslint忽略文件即可

```
dist
node_modules
public
.husky
.vscode
.idea
*.sh
*.md

src/assets

.eslintrc.cjs
.prettierrc.cjs
.stylelintrc.cjs
```





### 2. 配置Prettier

首先安装一下vscode的Prettier插件，然后安装Prettier包：

```bash
# 下载
yarn add prettier -D
```

安装成功后，项目根目录创建`.prettierrc.cjs`，并配置相关代码格式：修改完毕后记得重启一下VSCode才能生效。

```json
module.exports = {
  //单行长度
  printWidth: 120,
  // 指定每个缩进级别的空格数
  tabWidth: 2,
  // 使用制表符而不是空格缩进行
  useTabs: false,
  // 在语句末尾打印分号
  semi: false,
  // 使用单引号而不是双引号
  singleQuote: true,
  // 多行时尽可能打印尾随逗号。（例如，单行数组永远不会出现逗号结尾。） 可选值"<none|es5|all>"，默认none
  trailingComma: 'none',
  // 在对象文字中的括号之间打印空格
  bracketSpacing: true,
  // 在单独的箭头函数参数周围包括括号 always：(x) => x \ avoid：x => x
  arrowParens: 'avoid',
  // 指定要使用的解析器，不需要写文件开头的 @prettier
  requirePragma: false,
  // 不需要自动在文件开头插入 @prettier
  insertPragma: false,
  // 换行设置 always\never\preserve
  proseWrap: 'never',
  // 指定HTML文件的全局空格敏感度 css\strict\ignore
  htmlWhitespaceSensitivity: 'css',
  // Vue文件脚本和样式标签缩进
  vueIndentScriptAndStyle: false,
  // 换行符使用 lf 结尾是 可选值"<auto|lf|crlf|cr>"
  endOfLine: 'lf'
}
```

接着在 `package.json` 中的 script 项添加格式化指令，这样就可以运行指令格式化：

```json
"scripts": {
  ...
    "lint:prettier": "prettier --write \"./**/*.{js,cjs,ts,json,tsx,css,less,scss,vue,html,md}\"",
}
```

最后在项目根目录下添加 `.prettierignore` 配置prettier忽略文件即可

```
dist
node_modules
public
.husky
.vscode
.idea
*.sh
*.md

src/assets
```

我们也可以在设置中开启 `Editor: Format On Save`，并右键将默认格式化程序修改成prettier，这样就会自动在保存时进行代码格式化当前文件。



### 3. 解决冲突

事实上，`eslint`可能会与`prettier`产生冲突，例如`eslint`配置使用单引号，但`prettier`中配置了使用双引号，这样在保存时就会导致后生效的一方覆盖了前面的修复，产生冲突。

冲突的本质在于 `eslint`既负责了代码质量检测，又负责了一部分的格式美化工作,格式化部分的部分规则和 `prettier`不兼容。

解决办法是通过两个库抵消冲突：让`eslint`只负责代码质量检测而让`prettier`负责格式美化

- `eslint-plugin-prettier` 将 Prettier 的规则设置到 ESLint 的规则中。

- `eslint-config-prettier` 关闭 ESLint 中与 Prettier 中会发生冲突的规则。（默认是先eslint格式化，再Prettier格式化）

```bash
yarn add eslint-plugin-prettier eslint-config-prettier -D
```



安装好之后，只需要在.eslintrc.cjs中的extends里加`"plugin:prettier/recommended"`，注意这一行要加在最后

```json
module.exports = {
  ...
  extends: [
    ...
    'plugin:prettier/recommended' // 加在最后
  ],
  ...
}
```

注意此时可能整个文件都会飘红，这是因为 `eslint-plugin-prettier` 将 Prettier 的规则设置到 ESLint 的规则中，导致对cjs文件的校验规则变化了，例如要使用单引号等，我们只需要右键将 Prettier 设置为默认格式化工具，再格式化一次即可修复。



### 4.配置stylelint

[stylelint](https://www.stylelint.com.cn/)为css的lint工具。可格式化css代码，检查css语法错误与不合理的写法，指定css书写顺序等...

安装相关依赖包:使用 less 或 sass 自动修改对应包名即可

```bash
yarn add sass stylelint postcss postcss-scss postcss-html stylelint-config-recommended-scss stylelint-config-standard stylelint-config-standard-vue stylelint-scss stylelint-order -D
```

依赖说明：

**stylelint**: `css`样式lint工具

**postcss**: 转换`css`代码工具

**postcss-scss**: 识别`less`语法

**postcss-html**: 识别html/vue 中的`<style></style>`标签中的样式

**stylelint-config-standard**: `Stylelint`的标准可共享配置规则，详细可查看官方文档

**stylelint-config-recommended-scss**: `sass`的推荐可共享配置规则，详细可查看官方文档

**stylelint-config-standard-vue**: lint`.vue`文件的样式配置

**stylelint-scss**: `stylelint-config-recommended-sass`的依赖，`sass`的`stylelint`规则集合

**stylelint-order**: 指定样式书写的顺序，在`.stylelintrc.js`中`order/properties-order`指定顺序

接着在项目中添加 `.stylelintrc.cjs` 配置文件：

```js
module.exports = {
  extends: [
    'stylelint-config-standard',
    'stylelint-config-recommended-scss',
    'stylelint-config-standard-vue'
  ],
  plugins: ['stylelint-order'],
  // 不同格式的文件指定自定义语法
  overrides: [
    {
      files: ['**/*.(scss|css)'],
      customSyntax: 'postcss-scss'
    },
    {
      files: ['**/*.(html|vue)'],
      customSyntax: 'postcss-html'
    }
  ],
  ignoreFiles: ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts', '**/*.json', '**/*.md', '**/*.yaml'],
  rules: {
    'no-descending-specificity': null, // 禁止在具有较高优先级的选择器后出现被其覆盖的较低优先级的选择器
    'selector-pseudo-element-no-unknown': [
      true,
      {
        ignorePseudoElements: ['v-deep']
      }
    ],
    'selector-pseudo-class-no-unknown': [
      true,
      {
        ignorePseudoClasses: ['deep']
      }
    ],
    // 指定样式的排序
    'order/properties-order': [
      'position',
      'top',
      'right',
      'bottom',
      'left',
      'z-index',
      'display',
      'justify-content',
      'align-items',
      'float',
      'clear',
      'overflow',
      'overflow-x',
      'overflow-y',
      'padding',
      'padding-top',
      'padding-right',
      'padding-bottom',
      'padding-left',
      'margin',
      'margin-top',
      'margin-right',
      'margin-bottom',
      'margin-left',
      'width',
      'min-width',
      'max-width',
      'height',
      'min-height',
      'max-height',
      'font-size',
      'font-family',
      'text-align',
      'text-justify',
      'text-indent',
      'text-overflow',
      'text-decoration',
      'white-space',
      'color',
      'background',
      'background-position',
      'background-repeat',
      'background-size',
      'background-color',
      'background-clip',
      'border',
      'border-style',
      'border-width',
      'border-color',
      'border-top-style',
      'border-top-width',
      'border-top-color',
      'border-right-style',
      'border-right-width',
      'border-right-color',
      'border-bottom-style',
      'border-bottom-width',
      'border-bottom-color',
      'border-left-style',
      'border-left-width',
      'border-left-color',
      'border-radius',
      'opacity',
      'filter',
      'list-style',
      'outline',
      'visibility',
      'box-shadow',
      'text-shadow',
      'resize',
      'transition'
    ]
  }
}

```

然后同样在 `package.json` 中的 script 项添加格式化指令，这样就可以运行指令格式化样式：

```json
"scripts": {
  "lint:stylelint": "stylelint \"./**/*.{css,scss,vue,html}\" --fix"
}
```

此外我们还可以安装vscode插件 `Stylelint`,从而实现保存时自动格式化样式：

```json
// 开启自动修复
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true,
    "source.fixAll.stylelint": true
  },
// stylelint校验的文件格式
  "stylelint.validate": ["css", "scss", "vue", "html"]
```

最后添加上 `.stylelintignore` 校验忽略配置文件即可

```
dist
node_modules
public
.husky
.vscode
.idea
*.sh
*.md

src/assets
```



### 5. 全局风格

上面的相关配置基本都是针对前端文件的，如果我们还想要对一些其他类型的文件风格进行限制，我们可以使用 `.editorconfig` 配置文件

VSCode插件里安装 **EditorConfig for VS Code**，插件下载完毕后在项目根目录下创建 `.editorconfig` 文件进行配置。此插件配置优先级高于vscode的默认配置。

```bash
root = true

[*] # 表示所有文件适用
charset = utf-8 # 设置文件字符集为 utf-8
indent_style = space # 缩进风格（tab | space）
indent_size = 2 # 缩进大小
end_of_line = lf # 控制换行类型(LF | cr | crlf)
trim_trailing_whitespace = true # 是否从行尾删除空格
insert_final_newline = true # 文件是否应以换行符结尾

[*.md] # 表示仅 md 文件适用以下规则
max_line_length = off # 在指定的字符数后强制硬换行
trim_trailing_whitespace = false
```



### 6.配置husky与lint-staged

虽然上面已经配置好了`eslint`、`preitter`与`stylelint`，但是还是存在一些问题：因为我们的一些自动保存格式化是借助vscode插件和修改配置项实现的，如果团队协作的其他开发者不使用vscode的，或者没有安装`eslint`、`preitter`与`stylelint`插件。就不能实现在保存的时候自动的去修复与和格式化代码。这样提交到`git`仓库的代码还是不符合要求的。所以我们需要在git提交前强制运行之前配置在`package.json`的各种修复指令。



`husky`是一个用来管理`git hook`的工具，git hook 即在我们使用git提交代码的过程中会触发的钩子。我们主要借助这个工具来实现提交前的自动修复。但如果我们直接使用之前配置修复指令，`husky`每次提交的修复都是整个项目的，对于前面已经修复过且没有改动的文件完全没必要。而`lint-staged` 由此诞生，它可以帮助我们检测到暂存区中的文件，从而只修复这一部分，提高效率。

```bash
yarn add husky lint-staged -D
```

然后在 `package.json` 中的 script 项添加一条指令,改指令会在装包时自动执行，进行husky的初始化。但现在我们已经不用初始化下载包。所以直接通过执行 `yarn prepare` ,在项目根目录下生成 `.husky` 配置文件夹。（如果没有生成则需要先初始化git仓库）

```json
"scripts": {
		...
    "prepare": "husky install"
},
```

然后通过命令添加`pre-commit`钩子文件，这个钩子文件就是让husky在我们git commit前进行拦截，并执行里面的指令

```bash
yarn husky add .husky/pre-commit
```

此时对文件里面进行添加指令即可，让我们执行`git commit`的时候执行package.json中配置的修复指令。并且当命令出现报错，就不会提交成功。以此来保证提交代码的质量和格式。

```sh
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# 这些是之前配置的全局命令，运行这些会对整个项目都进行修复
# yarn lint:eslint && yarn lint:prettier && yarn lint:stylelint
# 由于我们希望只修复暂存区文件，所以运行lint-staged命令
yarn lint:lint-staged
```

上面的配置会让我们在提交前执行 `lint-staged` 的修复指令，但我们还没有配置，所以我们要去`package.json` 中配置相关指令。`pre-commit`会执行`lint-staged`命令，这个命令在下方也已经配置好，就是对暂存区中不同类型的文件执行不同工具的修复指令。

```json
"scripts": {
		...
    "lint:lint-staged": "lint-staged"
},
"lint-staged": {
  "*.{js,ts}": [
    "eslint --fix",
    "prettier --write"
  ],
  "*.{cjs,json}": [
    "prettier --write"
  ],
  "*.{vue,html}": [
    "eslint --fix",
    "prettier --write",
    "stylelint --fix"
  ],
  "*.{scss,css}": [
    "stylelint --fix",
    "prettier --write"
  ],
  "*.md": [
    "prettier --write"
  ]
},
```



### 7.配置commitlint

事实上，在团队开发中我们也需要对 commit 信息进行规范化，不能每个人想怎么写就怎么写，此时就需要[commitlint](https://commitlint.js.org/#/guides-local-setup)) 进行规范化。安装commitlint脚手架和配置规则文件包：

```bash
yarn add @commitlint/cli @commitlint/config-conventional -D
```

然后在项目根目录下添加 `.commitlintrc.cjs` 配置文件：

```js
module.exports = {
  // 让commitlint继承@commitlint/config-conventional配置规则
  extends: ['@commitlint/config-conventional'],
  // 自定义规则
  // rules: {
  //   // @see https://commitlint.js.org/#/reference-rules

  //   // 提交类型枚举，git提交type必须是以下类型
  //   'type-enum': [
  //     2,
  //     'always',
  //     [
  //       'feat', // 新增功能
  //       'fix', // 修复缺陷
  //       'docs', // 文档变更
  //       'style', // 代码格式（不影响功能，例如空格、分号等格式修正）
  //       'refactor', // 代码重构（不包括 bug 修复、功能新增）
  //       'perf', // 性能优化
  //       'test', // 添加疏漏测试或已有测试改动
  //       'build', // 构建流程、外部依赖变更（如升级 npm 包、修改 webpack 配置等）
  //       'ci', // 修改 CI 配置、脚本
  //       'revert', // 回滚 commit
  //       'chore' // 对构建过程或辅助工具和库的更改（不影响源文件、测试用例）
  //     ]
  //   ],
  //   'subject-case': [0] // subject大小写不做校验
  // }
}
```

接着 利用 husky 的提交拦截钩子配置提交信息，配置完毕后当我们进行 `git commit` 时提交信息就必须按照默认规范来书写了,当然我们也可以自定义提交规则，参考官方文档即可。

```bash
npx husky add .husky/commit-msg  'npx --no -- commitlint --edit ${1}'
```



如果我们想更加自动化和省事。接下来，我们可以安装  [commitizen](https://github.com/commitizen/cz-cli) 辅助生成标准化提交信息 和 [cz-git](https://cz-git.qbb.sh/zh/guide/introduction) 适配器,即用来更换 commitizen 命令行工具的 **交互方式** 插件。

```bash
yarn add commitizen cz-git -D
```

接着在 `package.json` 中修改 commitizen 的适配器:

```json
{
  "scripts": {
		...
    "commit": "cz"
  },
  "config": {
    "commitizen": {
      "path": "node_modules/cz-git"
    }
  }
}
```

然后就可以向 `.commitlintrc.cjs` 配置文件添加自动流程信息了，可以参考[cz-git](https://cz-git.qbb.sh/zh/config/) 的配置模板。之后每次提交，我们在终端运行 `yarn commit` 即可开启提交流程。



## 配置环境变量

争对不同的开发环境，我们需要准备不同的环境变量，这样当我们运行不同环境启动或打包指令时，就能自动替换环境变量。具体可以查看 **vite笔记中的环境变量相关内容**

在项目根目录下创建下面文件：

- **.env** 文件（可选），全局的环境变量文件，所有环境都会使用到
- **.env.production** 文件，生产环境文件，在打包时会使用此文件里的环境变量
- **.env.development** 文件，开发环境文件，在本地开发时会使用此文件里的环境变量

注意，环境变量文件里的变量必须以 `VITE_` 开头，在项目中读取时通过 `import.meta.env.xxx` 获取。

```
## 开发环境

VITE_APP_TITLE = '后台管理系统（开发）'
VITE_APP_PORT = 3000
VITE_APP_BASE_API = '/dev-api'
```

如果我们想增加一些其他环境文件如 `.env.test` ,那么我们就需要在 `package.json` 中添加新的运行指令,通过 `--mode xxx` 指定运行环境，这样我们运行指令 `yarn dev:test` 就会读取 `.env.test` 环境文件了。

```json
"scripts": {
    "dev": "vite", // 默认读取.env.development
    "dev:test": "vite --mode test",
    "build": "vue-tsc && vite build", // 默认读取.env.production
    "preview": "vite preview"
  },
```



## 配置路径别名

我们可以在 `vite.config.ts` 中实现将 src路径 映射为 `@` ,方便后续文件引入。

如果找不到path模块，则需要安装开发依赖 `@types/node` 包

```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  // 设置路径别名
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src')
    }
  }
})
```

此时就已经可以将src替换为@了，但如果我们还想要路径智能提示，则还需要再 `tsconfig.json` 中添加如下配置：

```json
 "compilerOptions": {
   ...
   "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },
    ...
 }
```



## css预处理

vite生来就支持 less 和 sass 作为css预处理器，所以只需要安装自己习惯的处理器即可

```bash
yarn add sass -D
```

如果想向全局注入一些公共样式文件，则需要在 `vite.config.ts` 中进行配置

```ts
css: {
  // CSS 预处理器
  preprocessorOptions: {
    //define global scss variable
    scss: {
      additionalData: '@import "src/styles/globalVar.scss";@import "src/styles/globalMixin.scss";'
    }
  }
},
```

此外由于各种浏览器的默认样式，我们还需要进行清除：

```ts
yarn add normalize.css

// main.ts
import 'normalize.css'
```



## 组件自动导入

针对于全局组件自动导入，主要是利用了一个插件 **[unplugin-vue-components](https://github.com/antfu/unplugin-vue-components)**

```bash
yarn add unplugin-vue-components -D
```

之后只需要在 `vite.config.js` 中进行部分配置即可，更多配置参考 GitHub页面

```ts
// vite.config.ts
import Components from 'unplugin-vue-components/vite'

export default defineConfig({
  plugins: [
    Components({
      // dirs 指定自动导入的组件所在位置，默认为 src/components
      dirs: ['src/components/'],
      // 导入的配置文件生成位置,默认在根路径下生成
      dts: 'src/types/components.d.ts'
    }),
  ],
})
```

在使用ts时，注意需要让生成的 `components.d.ts` 在 `tsconfig.json` 中的 "include" 配置项中声明，由于我们将其放在了src下，默认已经包含，所以无需改动。

事实上，此插件还支持自动导入一些热门库的UI组件，例如 Element Plus 等，并且 Element Plus 官网上也推荐了这种按需自动导入的方法，详细配置请参考 [Element Plus](https://element-plus.gitee.io/zh-CN/guide/quickstart.html)



## API自动导入

针对API的自动导入，主要是利用了一个插件 **[unplugin-auto-import](https://github.com/antfu/unplugin-auto-import)**

```bash
yarn add unplugin-auto-import -D
```

之后只需要在 `vite.config.js` 中进行部分配置即可，更多配置参考 GitHub页面

```ts
// vite.config.ts
import AutoImport from 'unplugin-auto-import/vite'

export default defineConfig({
  plugins: [
    // ...
    AutoImport({
      imports: ['vue'], // 更多自动引入库在此添加
      dts: 'src/types/auto-imports.d.ts'
    }),
  ]
})
```

配置好之后 不需要再写`import { computed, ref } from 'vue'`等语句，该插件会帮助你按需自动导入vue，vue-router，pinia的api，开发体验更好。

与 `unplugin-vue-components` 一样，使用ts时，注意需要让生成的 `auto-imports.d.ts` 在 `tsconfig.json` 中的 "include" 配置项中声明，由于我们将其放在了src下，默认已经包含，所以无需改动。



配置完成使用后后， eslint可能会出现`no-undef`的错误，提示找不到自动导入的API。需要更新eslintrc相关配置。

```ts
AutoImport({
  ...
  eslintrc: { // 开启生成对eslint兼容文件
    enabled: true
  }
}),
```

再更新`eslintrc` ，重启项目生成配置文件即可解决

```js
// .eslintrc.js
module.exports = {
  extends: [
    './.eslintrc-auto-import.json', // 添加到最后
  ],
}
```



## SVG Icon使用

通过 [vite-plugin-svg-icons](https://github.com/vbenjs/vite-plugin-svg-icons) ,生成 svg 雪碧图,实现高性能预加载。

```bash
yarn add vite-plugin-svg-icons -D
```

之后在 `vite.config.ts` 中进行配置

```ts
import path from 'path'
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons'

export default defineConfig({
  plugins: [
    ...
    createSvgIconsPlugin({
      // 指定需要缓存的图标文件夹
      iconDirs: [path.resolve(process.cwd(), './src/icons')]
    }),
  ]
})
```

然后在 src/main.ts 内引入注册脚本,svg雪碧图就已经生成好了。

```ts
import 'virtual:svg-icons-register'
```

接下来就可以自定义组件进行使用 `src/components/SvgIcon.vue`：

```vue
<template>
  <svg aria-hidden="true">
    <use :xlink:href="symbolId" :fill="color" />
  </svg>
</template>

<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps({
  prefix: {
    type: String,
    default: 'icon'
  },
  name: {
    type: String,
    required: true
  },
  color: {
    type: String,
    default: '#333'
  }
})

const symbolId = computed(() => `#${props.prefix}-${props.name}`)
</script>

```

把svg图放在**src/icons**目录下,具体使用时引入注册即可，前面已经配置好了自定义组件(文件夹嵌套以短横线拼接)：

```html
<SvgIcon name="common-triangle" />
```



## mock模拟数据

前端开发接口数据往往没有现成的，所及就需要模拟数据来继续向下开发。[vite-plugin-mock](https://github.com/vbenjs/vite-plugin-mock/blob/main/README.zh_CN.md)

```bash
# 如果生产环境也需要模拟数据，mockjs则不能放在开发依赖中，具体看官方文档
yarn add mockjs vite-plugin-mock -D
```

然后在 `vite.config.ts` 中配置使用插件：

```ts
import { UserConfigExport, ConfigEnv } from 'vite'

import { viteMockServe } from 'vite-plugin-mock'
import vue from '@vitejs/plugin-vue'

export default ({
    plugins: [
      vue(),
      viteMockServe({
        mockPath: 'mock',
        enable: true
      })
    ],
})
```

之后在项目根目录下创建 **mock** 文件夹，然后在里面书写模拟的请求数据即可，具体写法参考 [vite-plugin-mock](https://github.com/vbenjs/vite-plugin-mock/blob/main/README.zh_CN.md) 官方文档。

如果出现报错（包版本3.0.0），显示 `require is not defined` ，这只因为我们在package.json中配置了 `"type": "module"`, 导致vite只解析ES模块的导入导出，不识别CommonJS的require。目前解决方法只能删去package.json中的那个设置，让vite按需解析。[#97](https://github.com/vbenjs/vite-plugin-mock/issues/97)

相同版本，修改mock文件夹下的文件，每次必须重新启动服务，不然会运行报错 [#98](https://github.com/vbenjs/vite-plugin-mock/issues/98)



## axios请求数据

在项目中，我们主要使用axios来发送请求，并主要使用他的请求/响应拦截器。

```bash
yarn add axios
```

然后在src目录下创建 `utils/request.ts`， 在此封装我们的请求工具。

```ts
import axios, { InternalAxiosRequestConfig, AxiosResponse } from 'axios'

// 创建 axios 实例
const service = axios.create({
  baseURL: import.meta.env.VITE_APP_BASE_API,
  timeout: 50000,
  headers: { 'Content-Type': 'application/json;charset=utf-8' }
})

// 请求拦截器
service.interceptors.request.use(
  (config: InternalAxiosRequestConfig) => {
    return config
  },
  error => {
    return Promise.reject(error)
  }
)

// 响应拦截器
service.interceptors.response.use(
  (response: AxiosResponse) => {
    return response
  },
  error => {
    return Promise.reject(error.message)
  }
)

// 导出 axios 实例
export default service
```



## vue-router 路由

先下载安装：

```bash
yarn add vue-router
```

在项目的src目录下创建 `router/index.ts` 和 `router/routes.ts` 

```ts
// index.ts
import { createRouter, createWebHashHistory } from 'vue-router'
import constantRoutes from './routes'

const router = createRouter({
  history: createWebHashHistory(),
  routes: constantRoutes,
  // 刷新时，滚动条位置还原
  scrollBehavior: () => ({ left: 0, top: 0 })
})

export default router
```

```ts
// routes.ts
import { RouteRecordRaw } from 'vue-router'

const constantRoutes: RouteRecordRaw[] = [
  {
    path: '/login',
    name: 'login',
    component: () => import('@/views/login/app-login.vue')
  },
  {
    path: '/',
    name: 'layout',
    component: () => import('@/views/home/app-home.vue')
  },
  {
    path: '/404',
    name: '404',
    component: () => import('@/views/error-page/page-404.vue')
  },
  // 匹配前面未定义的路由，重定向到404
  {
    path: '/:pathMatch(.*)*',
    redirect: '/404'
  }
]

export default constantRoutes
```

最后在 `main.ts` 里注册路由即可,在 `App.vue` 里放上路由出口即可：

```ts
import router from '@/router/index'

createApp(App).use(router).mount('#app')
```



## pinia 仓库

```bash
yarn add pinia
```

然后在项目src目录下创建 `store/index.ts` 创建仓库，在 `main.ts` 里注册路由即可

```ts
import { createPinia } from 'pinia'

const store = createPinia()
export default store

// main.ts
import store from '@/store/index'
createApp(App).use(store).mount('#app')
```

之后就可以在 `store/modules/` 下创建分类小仓库供我们使用了；具体使用方法参考 [pinia官方文档](https://pinia.vuejs.org/zh/)

```ts
// src/store/modules/user.ts

import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', () => {
  // 仓库存取用户token
  const token = ref<string | null>(localStorage.getItem('token'))

  /**
   * 用户登录方法
   */
  function userLogin(form: loginForm) {
    // ...
  }

  return { token, userLogin }
})
```

此外的一个问题就是如果页面刷新会导致仓库初始化，数据都变为初始值。对于一些简单数据我们可以直接用 `localStorage` 存储（注意对象的json转化），初始化时直接读取。若想要大范围持久存储，可以使用pinia持久化插件 [pinia-plugin-persistedstate](https://prazdevs.github.io/pinia-plugin-persistedstate/zh/guide/) 具体使用方式查看官方文档。
