# UEToADB
UnreadEngine To  Application Development Base

## 目的
通过修改 虚幻引擎的 一些代码， 将其改造成一个便于开发3d应用的基础。比如说带3d的一些设计应用。一般情况下，只需要修改虚幻引擎的源码中source文件夹的一部分模块，就可以实现一个新的3d应用，但虚幻引擎本身很笨重，十分占用硬盘空间。如果每创建一个应用就创建一个新的虚幻引擎源码目录，实在是太浪费硬盘空间。还有就是对照正常的unrealEditor进行比较，方便查找问题。这个项目就是为了解决这个问题，在复用所有不需要修改的内容的情况下，既不影响原有的工程，又能不断创建新的工程。

最终到达的效果是，控制调用脚本（GenerateProjectFilesNew.bat) 传入2个参数（新源码目录名和sln文件名），就会在 Engine 目录下 创建一个新源码目录，并将 Runtime，Develop， Editor 三个目录拷贝复制过去，并生成新的工程。打开工程后就可以得到一个新的默认应用。这个默认应用会共享原Source目录下的Programs和ThirdParty,以及Engine/Plugins 目录下的插件。通过Target.cs文件的设置（	bBuildAllModules = false;bBuildAllPlugins = false;）, 这个应用只会加载所需模块，以及指定的插件。使用注意有详细介绍。

## 支持平台
* windows
## 测试通过版本 (vs2019)
* UnrealEngine-4.27.2-release
* UnrealEngine-5.0.0-early-access-2

## 使用步骤 （windows）
* 准备：按照虚幻引擎官方教程，下载源码。运行setup.bat安装依赖。安装python3.
* 把本仓库所有文件放到源码根目录下。
* 运行python changeUBT.py, 使用字符串替换的方式修改 UBT这个工具的源码。
  添加了一个新的目标类型App, 通过传入参数的方式设置新的工程目录。然后让UBT能正确的加载模块。
* 运行python changeSource.py, 使用字符串替换的方式修改 Source目录及Plugins目录下的部分文件内容。
  主要是修改模块设置文件（*build.cs）里的目录引用路径。

  
* (UE5) 使用控制台运行 GenerateProjectFilesNew.bat, （新源码目录名和sln文件名），就会在 Engine 目录下 创建一个新源码目录，将 Runtime，Develop， Editor 三个目录拷贝复制过去，并生成新的sln。
* (UE4) 使用控制台运行 GenerateProjectFilesNewUE4.bat, （新源码目录名和sln文件名），就会在 Engine 目录下 创建一个新源码目录，将 Runtime，Develop， Editor 三个目录拷贝复制过去，并生成新的sln。

![image](https://user-images.githubusercontent.com/5336757/153746555-c5210cb5-1097-4e47-b146-978a2828cbb3.png)
  
* 一切顺利如下图

![image](https://user-images.githubusercontent.com/5336757/153746634-9c9fac70-b5fc-4ab9-8d9c-8c9f3f360c53.png)
![image](https://user-images.githubusercontent.com/5336757/153749630-86758665-a360-49ac-bd29-2a8f8aff4027.png)


* 双击sln,打开vs.打开sln工程后就可以得到一个新的默认应用。这个应用会共享 原本的Source目录下的 Programs和 ThirdParty 以及插件目录。 
  修改一下解决方案配置（选择后面带App的）和解决方案平台（win64)。选择启动项目TestApp，启动调试器，经过一段长时间的编译，就会得到一个默认的编辑器。
  
  ![image](https://user-images.githubusercontent.com/5336757/153746762-63429b28-d2f7-45cf-925a-ed7a4e075362.png)
  
* 修改新源码目录下的源码，得到你想要的应用。
* 控制台运行 GenerateProjectFilesNew.bat或GenerateProjectFilesNewUE4.bat  新源码目录名 sln文件名，得到新的工程。
## 注意事项
  原本的UnrealEditor目标默认是编译所有模块及插件的，十分耗费时间。为了减少编译时间，在TestApp.build.cs里 设置了	bBuildAllModules = false;
  并修改了ubt内部代码，使新目标默认是只加载相关模块。
  新添加了一种加载插件的方式。通过设置bBuildAllPlugins = false; 目标会不一下加载所有插件。而是通过给 EnablePlugins这个变量容器添加插件名 来指定需要加载的插件。
  比如当编辑器打开 uproject工程时，一般会要求编辑器程序中包含 uproject里设置的插件模块。这时可以通过在TestApp.build.cs中添加 给 EnablePlugins 添加模块来解决这个问题。
  .uproject文件里需添加	"DisableEnginePluginsByDefault": true, "Plugins" 字段设置的插件必须包含在 EnablePlugins 里，不然会报无法找到插件的错误。
  即可以选择像原有的一样加载所有插件，也可以指定需要的一些插件加载，减少编译时间。这个地方根据自己的需要进行修改。
  
  ![image](https://user-images.githubusercontent.com/5336757/153750442-fd4f5d09-a000-4565-a536-a759e97d06d6.png)


