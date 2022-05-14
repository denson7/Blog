
 ## 环境准备
### Node版本
由于 N-API 是 Node 在 8.x 版本提出的，所以要使用这一新特性，理论上安装 8.x 以上的版本即可。 但是，8.x 版本提出的时候，该特性还处于试验阶段，因此，最好的版本建议是 10.x 以上。
```bash
$ node -v
v12.14.1
```
### 安装node-gyp
node-gyp 是用 Node 编写的一个跨平台命令行工具，主要用于编译 Node 的原生模块。 它依据 binding.gyp 配置文件，将原生模块文件编译成 .node 文件，也就是动态链接库。
```bash
$ npm i -g node-gyp
+ node-gyp@9.0.0
updated 1 package in 4.202s
```
### 安装编译工具
由于 node-gyp 在编译时，依赖于不同平台的编译器，因此，针对不同平台，还需要安装不同的编译工具。此处仅实例window系统
```bash
$ npm install --global --production windows-build-tools
// .npmrc
msvs_version=2017
```
## 演示HelloWorld
### Step 1 引入头文件
首先新建一个文件，命名为 test.cc，并引入依赖的头文件
```c++
#include <node_api.h>
#include <stdio.h>
```
说明：
node_api.h 这个头文件，提供了 N-API 封装的函数，是开发 C/C++ 模块的基础。 
stdio.h，主要用来处理输入输出问题。
### Step2 编写逻辑函数
比如：定义一个 SayHello 的函数
```c++
napi_value SayHello(napi_env env, napi_callback_info info)
{
    printf("Hello World\n");
    return NULL;
}
```
说明：
napi_value表示 javascript 类型
napi_env 表示上下文变量
napi_callback_info 代表传递给回调函数的一个封装的数据类型，可以用于获取有关调用时的上下文信息，也可以用于设置回调函数的返回值

### Step3 模块注册
```c++
napi_value Init(napi_env env, napi_value exports)
{
  napi_status status;
  napi_value fn;
 
  status = napi_create_function(env, NULL, 0, SayHello, NULL, &fn);
  if (status != napi_ok)
    return NULL;

  status = napi_set_named_property(env, exports, "sayHello", fn);
  if (status != napi_ok)
    return NULL;

  return exports;
}
```
说明：
第 3 行，napi_status 是一个状态枚举，代表 N-API 函数的执行状态，若返回 napi_ok ，则表示执行成功。

第 6-8 行，将 C/C++ 函数通过 napi_create_function 转变成 Javascript 类型函数，其实也就是将 SayHello 赋值给了 fn 。

第 10-12 行，设置 exports 对象的一个属性为 sayHello，对应属性值为 fn，相当于 javascript 代码中的 exports.sayHello = fn； Init 函数最终返回一个 exports 对象，也就是最终导出的对象。

第 17 行，NAPI_MODULE 用于进行模块注册。注意到，这行代码并没有分号，因为，NAPI_MODULE 并不是一个函数，而是定义的一个宏。

采用 node-gyp 来构建插件，使用宏 NODE_GYP_MODULE_NAME 作为 NAPI_MODULE() 的第一个参数将确保会将最终二进制文件的名称传给 NAPI_MODULE()。

### Step4 构建 binding.gyp
构建前，需要先提供一个 binding.gyp 的配置文件，node-gyp 通过读取该配置文件来进行构建。
```json
{
   "targets": [{
       "target_name":"test",
       "sources": ["./test.cc"]
    }]
}


```
### Step5 生成.node文件
在 binding.gyp 同一目录下，执行构建命令，生成 .node 文件，这个文件将会被放进 build/Release 目录
```bash
$ node-gyp rebuild
gyp info it worked if it ends with ok
gyp info using node-gyp@9.0.0
gyp info using node@12.14.1 | win32 | x64
gyp info find Python using Python version 3.7.2 found at "D:\Soft\Env\Python3.7\python3.exe"
gyp info find VS using VS2017 (15.9.28307.1974) found at:
gyp info find VS "C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools"
gyp info find VS run with --verbose for detailed information
gyp info spawn D:\Soft\Env\Python3.7\python3.exe
gyp info spawn args [
gyp info spawn args   'D:\\Soft\\Env\\nvm\\v12.14.1\\node_modules\\node-gyp\\gyp\\gyp_main.py',
gyp info spawn args   'binding.gyp',
gyp info spawn args   '-f',
gyp info spawn args   'msvs',
gyp info spawn args   '-I',
gyp info spawn args   'F:\\caddon\\04\\build\\config.gypi',
gyp info spawn args   '-I',
gyp info spawn args   'D:\\Soft\\Env\\nvm\\v12.14.1\\node_modules\\node-gyp\\addon.gypi',
gyp info spawn args   '-I',
gyp info spawn args   'C:\\Users\\lds\\AppData\\Local\\node-gyp\\Cache\\12.14.1\\include\\node\\common.gypi',
gyp info spawn args   '-Dlibrary=shared_library',
gyp info spawn args   '-Dvisibility=default',
gyp info spawn args   '-Dnode_root_dir=C:\\Users\\lds\\AppData\\Local\\node-gyp\\Cache\\12.14.1',
gyp info spawn args   '-Dnode_gyp_dir=D:\\Soft\\Env\\nvm\\v12.14.1\\node_modules\\node-gyp',
gyp info spawn args   '-Dnode_lib_file=C:\\\\Users\\\\lds\\\\AppData\\\\Local\\\\node-gyp\\\\Cache\\\\12.14.1\\\\<(target_arch)\\\\node.lib',
gyp info spawn args   '-Dmodule_root_dir=F:\\caddon\\04',
gyp info spawn args   '-Dnode_engine=v8',
gyp info spawn args   '--depth=.',
gyp info spawn args   '--no-parallel',
gyp info spawn args   '--generator-output',
gyp info spawn args   'F:\\caddon\\04\\build',
gyp info spawn args   '-Goutput_dir=.'
gyp info spawn args ]
gyp info spawn C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\MSBuild\15.0\Bin\MSBuild.exe
gyp info spawn args [
gyp info spawn args   'build/binding.sln',
gyp info spawn args   '/clp:Verbosity=minimal',
gyp info spawn args   '/nologo',
gyp info spawn args   '/p:Configuration=Release;Platform=x64'
gyp info spawn args ]
在此解决方案中一次生成一个项目。若要启用并行生成，请添加“/m”开关。
  test.cc
  win_delay_load_hook.cc
    正在创建库 F:\caddon\04\build\Release\test.lib 和对象 F:\caddon\04\build\Release\test.exp
  正在生成代码
  All 7 functions were compiled because no usable IPDB/IOBJ from previous compilation was found.
  已完成代码的生成
  test.vcxproj -> F:\caddon\04\build\Release\\test.node
gyp info ok
```
### Step6 调用.node文件
生成 .node 文件后，我们新建test.js，并通过 require 来引入文件
```javascript
const demo = require('./build/Release/test.node')
demo.sayHello()
```
执行test.js，输出以下结果
```bash
$ node test.js
Hello World
```
完整文件
```c++
#include <node_api.h>
#include <stdio.h>

napi_value SayHello(napi_env env, napi_callback_info info)
{
  printf("Hello World\n");
  return NULL;
}

napi_value Init(napi_env env, napi_value exports)
{
  napi_status status; 
  napi_value fn;

  status = napi_create_function(env, NULL, 0, SayHello, NULL, &fn);
  if (status != napi_ok)
    return NULL;

  status = napi_set_named_property(env, exports, "sayHello", fn);
  if (status != napi_ok)
    return NULL;

  return exports;
}

NAPI_MODULE(NODE_GYP_MODULE_NAME, Init)

```



