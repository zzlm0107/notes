## 概述

在这里记录一下使用VSCode进行前端开发时遇到的一些配置问题和使用帮助，让在新环境中更快的得心应手的开发。此处默认是对VSCode有一定了解。



## 常用插件

**1. Chinese (Simplified) (简体中文)**

将vscode的页面语言改成中文。重启即可修改。



**2. EditorConfig for VS Code**

进行基础代码格式调整，方便团队样式统一。此插件配置优先级高于vscode的默认配置。

插件下载完毕后在项目根目录下创建 *.editorconfig* 文件进行配置。

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



**3. Ayu && Material Icon Theme**

颜色主题插件 Ayu，喜欢 *Ayu Mirage Bordered* 风格。视觉感观很舒适。  

图标主题插件 Material Icon Theme，利用小图标帮助区分不同类型的文件。



**4. GitLens**

git增强插件，可以文件每一行的提交信息，在左侧源代码管理分栏可以查看项目提交commit和成员等，非常好用。



**5. Live Server**

在本地开启一个在线服务器用于运行HTML文件，右键选择 *Open with Live Server* 即可，并且修改也会同步更新，简单调试必备。



**6. HTML CSS Support && JavaScript (ES6) code snippets**

提供HTML和CSS的智能补全，选择器验证的 HTML CSS Support；

以及 提供 ES6 及后续新语法的智能补全提示的 JavaScript (ES6) code snippets。



**7. ESLint && Prettier**

进行代码语法规范化检查的 ESLint 以及 进行代码样式格式化的 Prettier。

二者作为vscode插件虽然可以直接配置使用，但为了防止不同人的配置冲突，还是需要搭配npm包生成配置文件使用，先安装即可。



**8. Vetur && Vue Language Features (Volar)**

为 Vue2 提供语法智能支持的Vetur 以及 为 Vue3 提供智能语法提示的Volar。



**9. indent-rainbow**

为代码缩进提供一个彩虹条来帮助提升辨识度。



**10. Code Translate**

一个翻译插件，鼠标悬浮到代码上即可翻译单词，适合英语不太好的人使用。



## 文字样式

一款好的字体样式可以在开发时更加赏心悦目，所以我们可以简单配置一下VSCode的字体样式。

点开 *设置* 就可以进行基础配置：

- 字体大小根据个人观感修改即可；

- 字体族简单添加：**Consolas, '微软雅黑', 'Courier New', monospace**



## 代码片段

在项目开发中，我们可能会经常性重复的书写一些代码片段，如果每次都写可能会很麻烦，所以我们可以直接配置代码片段，到时候使用快捷键即可。

依次点击头部的 *文件 > 首选项 > 配置用户代码片段，* 可以选择是创建全局的代码片段还是当前项目的代码片段。

```json
{
  "Print to more-comment": { // 分类栏的标识提示
		"scope": "javascript,typescript", // 可用范围，空或省略则为全局范围
		"prefix": "more-comment", // 触发的关键字
		"body": [ // 代码片段体
			"/**",
			" * @Author : zzlm0107@163.com",
			" * @Date : ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE}",
			" * @Description : $1",
			" * @Param : $2",
			" * @Param : ${2:my}",
			" * @Param : ${3|one,two,three|}",
			" * @Return : $0类型声明",
			" */"
		],
		"description": "api文档里方法的注释片段" // 描述语句
	},
}
```



**常用语法**

1. 使用tab键，可以使编辑器光标在片段内移动。使用`$1`,`$2`指定光标位置。数字是每次制表位的顺序，而`$0`表示最终光标位置。`$`后面的数字相同时会被链接并同步更新。

2. 占位符是带有值的制表位，例如`${1:foo}`.占位符文本将被插入，以便可以轻松同步更改。占位符可以有选择值。语法为以逗号分隔的值枚举，用竖线字符括起来，例如`${1|one,two,three|}`.

3. 使用`${var}`，您可以插入变量的值。`var`是变量名称，具体如下：

   常用快捷变量：

   - `TM_CURRENT_LINE` 当前行的内容

   - `TM_FILENAME` 当前文档的文件名

   - `TM_FILENAME_BASE` 当前文档的文件名，不带扩展名

   - `CLIPBOARD `剪贴板的内容

   插入当前日期和时间：
   
   - `CURRENT_YEAR` 本年度
   - `CURRENT_YEAR_SHORT` 本年度的最后两位数
   - `CURRENT_MONTH` 两位数的月份（例如“02”）
   - `CURRENT_MONTH_NAME_SHORT` 月份的简称（例如“Jul”）
   - `CURRENT_DATE `两位数的月份日期（例如“08”），后面 时，分，秒规则相同
   - `CURRENT_SECONDS_UNIX` 自 Unix 纪元以来的秒数

   对于插入随机值：
   
   - `UUID` 版本 4 UUID
     
   
   对于插入行或块注释，尊重当前语言：
   
   - `BLOCK_COMMENT_START` 示例输出：在JS中 /\* 或 在HTML中 <!–
   - `BLOCK_COMMENT_END` 示例输出：在JS中 \*/ 或 在HTML中 –>

4. 此外对于一些特殊符号，当然也可以使用转义字符 `\` 进行转义。



## 相关设置

在vscode中存在全局设置和工作区设置，全局设置会影响到所有的项目，通过修改设置里的选项或 `settings.json` 即可, 工作区设置是放在项目根目录下的的 `.vscode` 文件夹下，文件名也是固定为`settings.json`。里面的相关配置只会影响当前工作区，并且优先级高于全局配置。

对于一些通用配置，如字体大小，样式等，推荐放在全局配置上，而一些如保存格式化，格式化工具等与项目绑定的配置推荐放在工作区设置上，避免不同项目的冲突。