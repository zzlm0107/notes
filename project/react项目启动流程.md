## react项目启动

```bash
yarn create vite my-react-app --template react-ts
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



操作完成后，会在项目根目录下会生成`.eslintrc.cjs` 配置文件。

1. 接着在 `package.json` 中的 script 项添加修复指令，这样就可以运行指令修复：

   ```json
   "scripts": {
     "dev": "vite",
     "build": "vue-tsc && vite build",
     "preview": "vite preview",
     "lint:eslint": "eslint . --ext .vue,.js,.ts,.jsx,.tsx --fix"
   },
   ```

2. 如果我们想在保存时自动修复eslint的错误，可以在settings.json中添加如下配置(需要eslint插件):

   ```json
   "editor.codeActionsOnSave": {
       "source.fixAll.eslint": true
   },
   ```

3. 此外我们可能会发现eslint在tsx文件中有一条校验规则提示我们必须要在文件作用域中导入react，但react导入后又会因为没有使用而提出警告。

   事实上，在`React@17`以后，是不需要再手动去引入`React`的。因为该版本之后加入了`react/jsx-runtime`，会自动对`JSX`进行解析。

   所以我们只需要告诉eslint即可

   ```js
   // .eslintrc.cjs
   
   module.exports = {
   	// ...
   	extends: [
   		"eslint:recommended",
       "plugin:react/recommended",
       // 加上这一条
       "plugin:react/jsx-runtime"
   	]
   	// ...
   }
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
    "plugin:prettier/recommended" // 加在最后
  ],
  ...
}
```



### 4.配置stylelint

[stylelint](https://www.stylelint.com.cn/)为css的lint工具。可格式化css代码，检查css语法错误与不合理的写法，指定css书写顺序等...

安装相关依赖包:使用 less 或 sass 自动修改对应包名即可

```bash
yarn add sass stylelint postcss postcss-scss postcss-html stylelint-config-recommended-scss stylelint-config-standard stylelint-config-prettier stylelint-scss stylelint-order -D
```

依赖说明：

**stylelint**: `css`样式lint工具

**postcss**: 转换`css`代码工具

**postcss-scss**: 识别`scss`语法

**postcss-html** 识别vue或html文件中的style标签中的样式语法

**stylelint-config-standard**: `Stylelint`的标准可共享配置规则，详细可查看官方文档

**stylelint-config-recommended-scss**: `sass`的推荐可共享配置规则，详细可查看官方文档

**stylelint-config-prettier**  `Stylelint`配置`prettier`避免冲突

**stylelint-scss**: `stylelint-config-recommended-sass`的依赖，`sass`的`stylelint`规则集合

**stylelint-order**: 指定样式书写的顺序，在`.stylelintrc.js`中`order/properties-order`指定顺序

接着在项目中添加 `.stylelintrc.cjs` 配置文件：

```js
module.exports = {
  extends: [
    'stylelint-config-standard',
    'stylelint-config-prettier',
    'stylelint-config-recommended-scss',
  ],
  plugins: ['stylelint-order'],
  // 不同格式的文件指定自定义语法
  overrides: [
    {
      files: ['**/*.(scss|css)'],
      customSyntax: 'postcss-scss'
    },
    {
      files: ['**/*.(html)'],
      customSyntax: 'postcss-html'
    }
  ],
  ignoreFiles: ['**/*.js', '**/*.ts', '**/*.json', '**/*.md', '**/*.yaml'],
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
        ignorePseudoClasses: ['deep','global']
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
  "lint:stylelint": "stylelint \"./**/*.{css,scss,tsx,html}\" --fix"
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
  "stylelint.validate": ["css", "scss", "tsx", "html"]
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







## css处理

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



由于react并没有提供类似vue的`scoped`方式避免样式的全局污染，所以当我们在react项目中导入外部样式时，一种常见的做法是利用**vite的css模块化功能**。

`xxx.module.css` ，不再直接引入，而是import模块化引入，使用时相当于对象的形式，样式类名变成了引入css模块的属性，这样vite在构建时就会在类名上加上一段hash字符串避免全局污染。

此外，借助global属性可以实现创建全局样式，搭配css模块化。我们只需在组件样式顶层采用模块化，其他子元素都创建全局样式避免模块化，那么就可以实现顶层的模块化管理着下层的类名，其他组件样式想要污染都会经过顶层hash类名过滤，从而无法选中。

```scss
// style.module.scss
// 顶层不修改，创建hash样式类名
.app-login {
  height: 100vh;
  background-image: url('@/assets/images/background.jpg');
  background-size: cover;

  // 子元素用：global包裹，避免模块化
  :global {
    .login-form {
      position: relative;
      top: 30vh;
      padding: 40px;
      width: 70%;
      background-image: url('@/assets/images/login_form.png');
      background-size: cover;

      > h1 {
        font-size: 40px;
        color: white;
      }
   }
}
```

```tsx
import style from './style.module.scss'

export default function AppLogin() {
  return (
    {/* 顶层采用模块化对象写法,子元素就可以正常书写 */}
    <div className={style['app-login']}>
      <div className='login-form'></div>
   	</div>
  )
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
      iconDirs: [path.resolve(process.cwd(), './src/assets/icons')]
    }),
  ]
})
```

然后在 src/main.ts 内引入注册脚本,svg雪碧图就已经生成好了。

```ts
import 'virtual:svg-icons-register'
```

接下来就可以自定义组件进行使用 `src/components/SvgIcon.tsx`：

```tsx
interface PropsType {
  name: string
  prefix: string
  color: string
  [propname: string]: unknown
}

export default function SvgIcon({ name, prefix = 'icon', color = '#333', ...props }: PropsType) {
  const symbolId = `#${prefix}-${name}`

  return (
    <svg {...props} aria-hidden="true">
      <use href={symbolId} fill={color} />
    </svg>
  )
}
```

把svg图放在**src/icons**目录下,具体使用时引入注册即可，前面已经配置好了自定义组件(文件夹嵌套以短横线拼接)：

```html
<SvgIcon name="common-triangle" />
```





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





## react-router-dom

在react项目中使用 `react-router-dom` 进行路由管理。

```bash
yarn add react-router-dom
```

一般项目都会进行路由懒加载处理，所以我们需要封装一个react的懒加载方法

```tsx
// router/lazyLoad.tsx
import React from 'react'
import { Suspense } from 'react'
import { Spin } from 'antd'

// 封装路由懒加载时的缓冲组件
const lazyLoad = (Comp: React.LazyExoticComponent<() => JSX.Element>): JSX.Element => {
  return (
    <Suspense
      fallback={
        <Spin
          size="large"
          style={{
            display: 'flex',
            alignItems: 'center',
            justifyContent: 'center',
            height: '100vh'
          }}
        />
      }
    >
      <Comp />
    </Suspense>
  )
}

export default lazyLoad
```

然后进行路由表的构建即可

```tsx
// router/routes.tsx
import { lazy } from 'react'
import { Navigate, useRoutes } from 'react-router-dom'
import lazyLoad from './lazyLoad'

const constantRoutes = [
  {
    path: '/login',
    element: lazyLoad(lazy(() => import('@/views/login/app-login')))
  },
  {
    path: '/',
    element: <Navigate to="/home" />
  },
  {
    path: '/home',
    element: lazyLoad(lazy(() => import('@/views/home/app-home')))
  }
]

const Router = () => {
  const routes = useRoutes(constantRoutes)
  return routes
}

export default Router
```

最后就是在App.tsx中使用路由。

```tsx
import { BrowserRouter } from 'react-router-dom'
import Router from '@/router/routes'

function App() {
  return (
    <BrowserRouter>
      <Router />
    </BrowserRouter>
  )
}

export default App
```





## redux toolkit

### 基础用法

由于原生redux使用起来的繁琐性，目前项目上常使用redux toolkit做状态管理，这也是官方推荐的一种做法，简化了redux的操作复杂度，变的更加容易上手。

下载安装：

```bash
yarn add react-redux @reduxjs/toolkit
```

首先，我们需要构建不同模块的小仓库

```ts
// store/modules/user.ts
import { createSlice } from '@reduxjs/toolkit'

// 定义仓库切片初始值与类型
const initialState: {
  token: string
} = {
  token: ''
}

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {
    // state是仓库切片，action.payload是触发时传递的数据
    setToken(state, action) {
      state.token = action.payload
    }
  }
})

export default userSlice.reducer

export const { setToken } = userSlice.actions
```

然后汇总小仓库，创建完整仓库

```tsx
// store/index.ts
// 汇总仓库切片，构建出完整的仓库
import { configureStore } from '@reduxjs/toolkit'
import userSliceReducer from './modules/user'

export default configureStore({
  reducer: {
    user: userSliceReducer
  }
})

```

最后在main.tsx中注入仓库即可

```tsx
import React from 'react'
import ReactDOM from 'react-dom/client'

import App from './App.tsx'

import { Provider } from 'react-redux'
import store from '@/store'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>
)
```



### 搭配axios

在开发中常见的一种操作是在仓库中发送请求获取数据然后存储起来，虽然redux toolkit提供了RTKQ发送请求，但常见的依然是使用axios。

由于请求的异步性，仓库切片中的reducers的异步操作会修改state，但此时内部state的代理对象已经被撤销，无法进行修改赋给原仓库，导致报错问题，所以我们需要使用 `createAsyncThunk` 钩子来进行异步操作。

[官网相关描述](https://cn.redux.js.org/tutorials/essentials/part-5-async-logic#%E4%BD%BF%E7%94%A8-createasyncthunk-%E8%AF%B7%E6%B1%82%E6%95%B0%E6%8D%AE)

```ts
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit'

import type { loginForm, userInfoType } from '@/api/user/type'
import { reqLogin } from '@/api/user/index'

// 定义仓库切片初始值与类型
const initialState: {
  token: string | null
  menuRoutes: Array<object>
  userInfo: userInfoType | null
} = {
  token: localStorage.getItem('token'),
  menuRoutes: [],
  userInfo: null
}

/**
 * 用户登录方法
 */
// 创建异步 thunk,相当于是一个异步的action
export const userLogin = createAsyncThunk('userLogin', async (loginForm: loginForm) => {
  const res = await reqLogin(loginForm)
  if (res.data.code === 200) {
    return res.data.data
  } else {
    throw new Error(res.data.msg)
  }
})

const userSlice = createSlice({
  name: 'user',
  initialState,
  reducers: {},
  // 根据异步action的状态进行不同的操作
  extraReducers: builder => {
    builder.addCase(userLogin.fulfilled, (state, action) => {
      state.token = action.payload
      localStorage.setItem('token', state.token)
    })
  }
})

export default userSlice.reducer
```

接下来在相关组件触发那个异步的action即可

```tsx
import { useState } from 'react'
import style from './style.module.scss'

import type { loginForm } from '@/api/user/type'
import { useDispatch } from 'react-redux'
import { ThunkDispatch, AnyAction } from '@reduxjs/toolkit'
import { userLogin } from '@/store/modules/user'

import { useNavigate } from 'react-router-dom'

export default function AppLogin() {
  // 定义接收异步action的类型
  const dispatch: ThunkDispatch<string, loginForm, AnyAction> = useDispatch()
  const navigate = useNavigate()

  const [loading, setLoading] = useState<boolean>(false)

  /**
   * 登录按钮点击回调
   */
  const onFinish = (values: loginForm) => {
    setLoading(true)
    try {
      // 触发异步action
      dispatch(userLogin(values))
      navigate('/')
    } catch (error) {
      console.log(error)
    } finally {
      setLoading(false)
    }
  }

  return (
    <div className={style['app-login']}></div>
  )
}

```

