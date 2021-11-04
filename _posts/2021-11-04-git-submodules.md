# git submodule

主项目中添加子项目的代码版本控制，主项目不必负责子项目的版本控制

## 添加子模块
`git submodule add <url> <path>`

**\<url\>:** 子模块仓库地址  
**\<path\>:** 子模块在当前工程下的路径

> git submodule add https://github.com/xxx/yyy.git ./src/main/resources/zzz

执行成功，工程根目录下会生成名为`.gitmodules`的文件，记录了子模块的信息。

## 更新子模块
如果是第一个update子模块，需要添加`--init`
`git submodule update --init --recursive`