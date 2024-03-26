## 切换淘宝源/还原默认源
### npm/yarn/pnpm
```sh
# 查看当前源
npm/yarn/pnpm get registry
# 修改为淘宝源
npm/yarn/pnpm config set registry https://registry.npmmirror.com/
# 还原到默认源
npm/yarn/pnpm config set registry https://registry.npmjs.org/
```

## nvm命令大全
```sh
# 显示帮助命令
nvm --help
# 查看nvm版本
nvm --version
# 查看版本列表
nvm list
nvm list installed
nvm list available
# 安装指定版本
nvm install 14.17.0
nvm install latest
# 使用指定版本
nvm use 14.17.0
# 卸载指定版本
nvm uninstall 14.17.0
# 查看当前使用node版本
nvm current
# 别名
nvm alias <name> <version>
nvm unalias <name>
# nvm缓存
nvm cache dir
nvm cache clear
# 开启关闭nvm管理
nvm on
nvm off
```

## pnpm 是凭什么对 npm 和 yarn 降维打击的
__npm深层嵌套__
早期 node 的 node_modules 包是深层嵌套的，导致：
1. 多个包之间会有公共依赖，深层嵌套的话，同样的依赖会复制很多次，占据比较大的磁盘空间
2. windows 的文件路径最长是 260 多个字符，这样嵌套是会超过 windows 路径的长度限制的

__yarn扁平化处理__
yarn 的解决方案是将深层嵌套的包铺平，只有少数包有嵌套规则，因为一个包是可能有多个版本的，提升只能提升一个，所以后面再遇到相同包的不同版本，依然还是用嵌套的方式。
npm 后来升级到 3 之后，也是采用这种铺平的方案了，和 yarn 很类似。
__扁平化导致的问题__
1. 幽灵依赖。由于包被扁平化了，导致明明没有声明在 dependencies 里的依赖，但在代码里却可以 require 进来。但是这样是有隐患的，因为没有显式依赖，万一有一天别的包不依赖这个包了，那你的代码也就不能跑了，因为你依赖这个包，但是现在不会被安装了。
2. 上面提到的依赖包有多个版本的时候，只会提升一个，那其余版本的包不还是复制了很多次么，依然有浪费磁盘空间的问题。

__pnpm通过link解决__
- 硬连接就是同一个文件的不同引用
- 软链接是新建一个文件，文件内容指向另一个路径

优点：
1. 节省磁盘，一个包全局只保存一份，剩下的都是软硬连接
2. 快，因为通过链接的方式而不是复制，自然会快

缺点：
1. 装一个包，依赖全部装到全局了，如果项目整个删除了，依赖还在全局目录中