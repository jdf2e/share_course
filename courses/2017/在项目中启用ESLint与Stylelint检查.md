# 在项目中启用 ESLint 与 Stylelint 检查

多人参与的项目中，经常会多人交叉编辑多个文件。这就导致了各个文件中充斥着各种编码风格。

最最常见的就有：

- 使用空格还是Tab
- 一次缩进是 2 个空格还是 4 个空格 还是 8 个空格
- 字符串使用单引号还是双引号
- JavaScript 一行结尾到底要不要加分号
- 关键字前后要不要加空格
- 等等等等

![](http://img12.360buyimg.com/uba/jfs/t6022/215/7744894545/87816/9f165c8c/5982a4eeNb64a52ea.png)

不同风格混杂在一起极大的影响代码的可读性与质量。因此在多人项目中维护一致的代码风格就很重要了。

本文就简单介绍一下如何通过 ESLint 和 Stylelint 为代码库配置针对 JavaScript 与 CSS（SCSS）的代码风格检查。

## 配置 ESLint

1. 安装 `eslint`

    ```
    yarn add --dev eslint
    // or
    npm install --save-dev eslint
    ```
    
2. 创建配置文件

    ```
    ./node_modules/.bin/eslint --init
    ```
    
    该命令会在项目目录中创建一个 `.eslintrc.json` 文件。默认生成的配置文件可能看起来是这个样子：
    
    ```
    {
        "rules": {
            "semi": ["error", "always"],
            "quotes": ["error", "double"]
        }
    }
    ```
    
    `rules` 里定义的就是你想要检查的规则了。例如上边示例中就配置了分号和引号的使用。
    
    完整的配置文档可以在[这里](http://eslint.org/docs/user-guide/configuring)找到。
    
3. 配置 npm script

    配置好以后就可以通过命令
    
    ```
    ./node_modules/.bin/eslint yourfile.js
    ```
    
    来执行检查了。每次都输入这么长串的命令有点反人类。所以还是加到 npm script 里。
    
    ```
    "eslint": "eslint src/js"
    ```
    
    这样以后可以通过
    
    ```
    npm run eslint
    // or
    yarn run eslint
    ```
    
    来执行了。
    
4. 引入 airbnb 风格的代码检查

    通过前三步已经可以正常使用 eslint 了。但是你需要针对很多详细的规则配置以生成一套自己的代码规范检查机制。更糟糕的是，当团队中有多名成员时可能每个人都有自己的习惯，很难统一，谁也不能说服其他人。
    
    针对以上情况，比较简单的方法就是直接引入大厂的代码风格。这里我们选择[Airbnb 的 JavaScript 代码规范](https://github.com/airbnb/javascript)。npm 中已经有了该规范的 eslint 配置版本。我们只需引入项目中即可。
    
    airbnb 规范在 npm 中有两个版本
    
    - `eslint-config-airbnb-base` 包含了针对 ES6+ 代码的检查
    - `eslint-config-airbnb` 在 `eslint-config-airbnb-base` 的基础上增加了对 react 和 jsx 语法的检查。
    
    以上两种根据自己实际情况选择即可。以下以 `eslint-config-airbnb` 为例演示。
    
    如果你使用 yarn，那么只需运行：
    
    ```
    // 先安装依赖
    yarn add --dev eslint-config-airbnb-base eslint-plugin-import eslint-plugin-react eslint-plugin-jsx-a11y
    
    // 安装 eslint-config-airbnb
    yarn add --dev eslint-config-airbnb
    ```
    
    如果你使用 npm，那么你需要先查看依赖包版本：
    ```
    npm info "eslint-config-airbnb@latest" peerDependencies
    ```
    
    得到输出：
    ```
    { eslint: '^3.19.0 || ^4.3.0',
      'eslint-plugin-jsx-a11y': '^5.1.1',
      'eslint-plugin-import': '^2.7.0',
      'eslint-plugin-react': '^7.1.0' }
    ```

    然后按照以上条件安装依赖包。
    
    最后安装 `eslint-config-airbnb`:
    
    ```
    npm install --save-dev eslint-config-airbnb`
    ```
    
    安装完成后，在 `.eslintrc.json` 中添加配置项：
    
    ```
    "extends": "airbnb"
    ```
    
    就大功告成啦。
    
## 配置 Stylelint
    
1. 安装 Stylelint 及插件
    
    ```
    yarn add --dev stylelint stylelint-order
    ```
    
    这里除了安装了 stylelint 自身外，还安装了一个 stylelint-order 插件。该插件的作用是强制你按照某个顺序编写 css。例如先写定位，再写盒模型，再写内容区样式，最后写 CSS3 相关属性。这样可以极大的保证我们代码的可读性。
    
2. 配置 Stylelint 规则

    这一点和 ESLint 很像。二者都只是提供了工具与规则。但如何配置这些规则完全取决于使用者。所以我们要根据需要自己引入或配置规则。
    
    这里我们为 Stylelint 选择了官方的代码风格 `stylelint-config-standard`。该风格是 Stylelint 的维护者汲取了 GitHub、Google、Airbnb 多家之长生成的。
    
    关于 stylelint-order 插件，官方并没有推荐配置。在 npm 上可以搜到[几份社区维护的配置](https://www.npmjs.com/search?q=stylelint+order+config)。我最终选择了 `stylelint-config-recess-order`
    
    ```
    yarn add --dev stylelint-config-standard stylelint-config-recess-order
    ```
    
    然后编辑配置文件 `.stylelintrc.json` 导入我们安装的配置：
    
    ```
    {
      "extends": ["stylelint-config-standard", "stylelint-config-recess-order"],
      "rules": {
        "at-rule-no-unknown": [true, {"ignoreAtRules" :[
          "mixin", "extend", "content"
        ]}]
      }
    }
    ```
    
    配置文件中单独配置 `at-rule-no-unknown` 是为了让 Stylelint 支持 SCSS 语法中的 `mixin`、`extend`、`content` 语法。
    
3. 配置 npm script

    在 package.json 中加入：
    
    ```
    "stylelint": "stylelint src/css/**/*.scss src/css/**/*.css"
    ```
    
    就可以啦。
    
## 小结

最后可以配置一条 npm script 命令同时执行 JavaScript 与 CSS 检查：

```
"lint": "run-p eslint stylelint
```

这里使用了 [npm-run-all](https://www.npmjs.com/package/npm-run-all) 模块中的 `run-p` 命令**并行**执行两个任务。
