# webpack4.x新版本笔记
网络上关于webpack的教程大多已经过时，由于webpack版本更新后许多操作变化很大，很多教程的经验已经不适合。当我们使用npm安装webpack时，若不指定webpack的版本，将默认安装最新版。

## 一、全局安装webpack
当然，按照个人习惯，我喜欢先install淘宝的npm镜像，这样来速度会快很多。

> npm install -g cnpm --registry=https://registry.npm.taobao.org

有关淘宝npm镜像的链接(https://npm.taobao.org/)

**全局安装webpack**

> cnpm install -g webpack

当执行该操作后，便在C:\Users\你的用户名\AppData\Roaming\npm\node_modules创建了webpack文件夹，里面存储了刚刚全局安装的webpack模块。

## 二、创建项目

项目初始化：

> cnpm init

这里，要求设置很多选项，可以按项目情况配置也可以不填直接回车。完成后，我们发现文件夹中增加了package.json文件，它用于保存关于项目的信息。

## 三、安装webpack-cli

我们在项目中本地安装webpack-cli：

> cnpm install -g webpack-cli

## 问题1：打包出现提示
我们在项目根目录新建一个文件hello.js，并在其中输入代码：

>function hello(str) {
    alert(str);
}
hello('hello world!');

在命令行输入：

webpack hello.js bundle.js

4.1.1版本提示：

```shell
The CLI moved into a separate package:webpack-cli.
Please install 'webpack-cli' in addition to webpack itself to use the CLI.
->when using npm: npm install webpack-cli -D
->when using yarn: yarn add webpack-cli -D
```

（注：这里-D参数和–save-dev的作用相同，只是一种简写而已。）
翻译成中文：

```shell
CLI（命令行工具）已经转移到了一个单独的包webpack-cli中。 
除了webpack自身外，请额外安装webpack-cli来使用CLI。 
-> 使用npm安装：npm install webpack-cli -D 
->使用yarn安装：yarn add webpack-cli -D
```
按照步骤三安装webpack-cli即可，否则便不能在命令行中使用webpack的相关命令。

**注意：必须是全局安装**

> cnpm install -g webpack-cli


五、设置模式
我们再次尝试打包：

webpack hello.js bundle.js
1
看样子似乎是可以运行了，但又出现了新的提示：

WARNING in configuration
The 'mode' option has not been set. Set  'mode' option to 'development' or 'production' to enable defaults for this enviroment.
ERROR in multi ./hello.js bundle.js
Module not found:ERROR:Can't resolve 'bundle.js' in 'C:/Users/你的用户名/Desktop/webpack-test'
@ multi ./hello.js bundle.js
1
2
3
4
5
翻译成中文：

配置警告： 
“mode”选项尚未设置。将“mode”选项设为“development”或“production”以启用此环境的默认设置。

multi错误 ./ hello.js bundle.js

未发现模块：错误：无法解析’C:/Users/你的用户名/Desktop/webpack-test’中的bundle.js

@ multi ./hello.js bundle.js

这里提示我们存在的第一个问题是没有配置webpack的mode选项，默认有production和development两种模式可以设置，因此我们尝试设为development模式，在命令行输入：

webpack --mode development
1
我们看到进行了打包并显示了Hash、Version、Time、Build at信息，表明已经可以打包。不过，仍然有错误提示：

ERROR in Entry module not found:ERROR:Can't resolve './src' in 'C:/Users/你的用户名/Desktop/webpack-test'
1
翻译成中文：

未找到入口模块发生错误：错误：无法解析’C:/Users/你的用户名/Desktop/webpack-test’中的’./src’

六、创建入口文件
这表明webpack4.x是以项目根目录下的'./src'作为入口，但我们的项目中缺乏该路径，因此我们在根目录下创建src文件夹，事实上webpack4.x以'./src/index.js'作为入口，单单创建src文件而没有index.js文件仍然会报错，因此我们

将hello.js移动到'./src'，并重命名为index.js。

现在如果我们再次执行

webpack index.js bundle.js
1
会提示can.t resolve相关的错误。

原因是，webpack4.x的打包已经不能用webpack 文件a 文件b的方式，而是直接运行webpack --mode development或者webpack --mode production，这样便会默认进行打包，入口文件是'./src/index.js'，输出路径是'./dist/main.js'，其中src目录即index.js文件需要手动创建，而dist目录及main.js会自动生成。 
因此我们不再按webpack 文件a 文件b的方式运行webpack指令，而是直接运行

webpack --mode development
1
或者

webpack --mode production。
1
这样便能够实现将'./src/index.js'打包成'./dist/main.js'。 
不过每次都要输入这个命令，非常麻烦，我们在package.json中scripts中加入两个成员：

"dev":"webpack --mode development",
 "build":"webpack --mode production"
1
2
以后我们只需要在命令行执行npm run dev便相当于执行webpack --mode development，执行npm run build便相当于执行webpack --mode production。 
我们在根目录执行：

npm run dev
1
可以看到根目录下生成了dist目录，并且dist目录下生成了main.js文件，main.js文件已经打包了src目录下index.js文件的代码。

七、配置其他参数
我们如果需要配置webpack指令的其他参数，只需要在webpack –mode production/development后加上其他参数即可，如：

webpack --mode development --watch --progress --display-modules --colors --display-reasons
1
当然，这也可以写入package.json的scripts之中。

八、总结
我们可以将以上探索进行整理总结，首先是注意事项：

1、webpack-cli必须要全局安装，否则不能使用webpack指令； 
2、webpack也必须要全局安装，否则也不能使用webpack指令。 
3、webpack4.x中webpack.config.js这样的配置文件不是必须的。 
4、默认入口文件是./src/index.js，默认输出文件./dist/main.js。

配置步骤：

1、创建工程目录； 
2、初始化工程目录：npm init。 
3、全局安装webpack-cli。 
4、全局安装webpack。 
5、webpack –mode development/production进行打包，可在package.json中配置dev和build的脚本，便只需运行npm run dev/build，作用相同。 
6、在webpack –mode development/production可串联设置其他参数。
