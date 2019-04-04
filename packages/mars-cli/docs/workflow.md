# mars-cli 的执行流程

## create 指令

create 指令直接执行了 vue-cli 中的 create 方法：

```javascript
const create = require('@vue/cli/lib/create');
await create(name, options);
```

因此整个执行流程与 vue-cli 是相同的。

文件产出使用了单独的库，@marsjs/cli-template 作 vue-cli 的插件。 由于 vue-cli 默认一定会加载 cli-service 这个插件，产出了大量无用内容，因此在 create 完成后，对无用文件进行了删除。

执行 create 命令时，提示用户选择创建的项目是否需要支持 h5，之后修改 preset，将参数传给 @marsjs/cli-template。@marsjs/cli-template 会根据参数 render 不同的文件内容。

- mars-cli-template/generator/template: 不支持 h5 的项目文件
- mars-cli-template/generator/template-h5: 不支持 h5 的项目文件

> TODO: 将支持 h5 的项目模板和不支持 h5 的项目模板进行合并，抽取公共内容。

除此之外，还有一个 dist-h5 文件夹，这个文件夹为产出 vue 工程所需要的一些文件，不是通过 vue-cli 的 generator 进行 render 的，是直接进行了文件拷贝。拷贝发生在每次执行 h5 编译时，判断文件不存在才会拷贝。

### npm 脚本

产出的项目中，默认在 package.json 中提供了到多端的 build 和 serve 脚本。

另外如果是支持 h5 的项目，还有一个 build-dist-h5 脚本，这个脚本用于在 mars 编译产出 vue 工程后，使用 vue-cli-service 对 vue 工程进行编译，一般不需要执行。

至于为什么会需要使用 vue-cli-service 对 vue 工程进行编译，见下方 build 指令。


## build、serve 指令

build 和 serve 指令的执行流程基本相同，使用的是 mars-cli/lib/script/runjs 中的脚本，内部使用 gulp 对小程序进行编译。

### h5 的编译

需要注意的是目前 h5 编译需要两步，小程序项目会通过 mars-build 先编译为 vue 工程，之后需要使用 vue-cli-service 对 vue 工程进行编译。


## mars-cli-service
