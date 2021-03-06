前提知识：有基本的Egret项目开发经验，会部署Android开发环境

技术等级：中级

必需产品：Egret Engine 1.5+([下载地址](http://www.egret.com/products/engine.html))

Egret开发工具：WebStorm或Visual Studio + EgretVS ([下载地址](http://www.egret.com/products/others.html))

Android开发工具：IntelliJ IDEA

----

### 说明

Android封装Egret项目，即为之前所说的Android打包App。也就是让原本只能在浏览器运行的Egret项目变身为Android平台的App。不再依赖浏览器运行，而是像传统的Android App一样，直接运行在Android平台上。

----

### 为何建立Egret 的Android封装项目

对Android开发比较熟悉的朋友会觉得绕弯路了，用Android直接开发岂不更方便？本教程开始前，我们分别对不同偏向的两种开发者进行分析，搞清为什么做，再学习怎么做。


### 对于原生Android开发者：

1) 开发效率高，TypeScript本身的高级语言特性带来的开发效率提高，毋庸多言。
2) 开发API友好。 Egret引擎使用AS3 API体系架构，是当前游戏业界开发2D游戏公认最友好方便，也是最先进的API体系，使开发游戏不再需要在臃肿的API中反复考量，集中精力专注具体的游戏需求实现。
3) 跨平台开发。移动开发最理想的情况就是一个项目一套代码，多平台发布。Egret项目无论运行于浏览器、打包Android App还是iOS App均使用同一个Egret项目，当然也是同一套代码。

### 对于Egret标准项目开发者：

由于建立Android App所使用的Egret-Android-Support本身实质是基于Egret runtime来运行的，所以对于Egret标准项目开发者，建立Android App的优势更加明显：
1) runtime使用Native方式渲染，将Egret标准项目封装AndroidApp后将使游戏性能得到质的飞跃。
2) runtime解决了碎片化的问题，因此不同的Android设备上对H5播放多声道声音的支持差异，不同Android版本对于硬件加速的支持差异，均统一得到了解决，统统都支持！
3) 更多runtime带来的提升可参考 [Egret Runtime 白皮书](http://www.egret.com/products/runtime.html)。

----

### 从标准的Egret项目开始

本教程整体的目的是将 Egret 创建的可运行 H5 游戏，封装打包为一个可在 Android 真机运行的游戏 App。

目前的封装方式，实质上是需要额外建立一个 Android 项目，然后通过一些操作来使其可以运行 Egret 标准项目。这样的项目在本文中称之为 Android 封装项目。

基于这样的封装方式，包含两部分，一个是Egret标准项目，另一个是 Android 封装项目。Egret 标准项目的目的是建立所有的游戏运行及渲染逻辑，Android 封装项目的目的是将 Egret 标准项目封装并且生成标准的 Android APK 包，安装后运行 Egret 标准项目的内容。

为了便于管理，我们设计为一个主项目包含两个子项目并列的结构。 设我们要做的游戏项目名为`runner`（当然你可以根据自己喜好采用别的项目名称，后文中保持一致即可），则我们以此在工作空间建立主项目目录，名为`runner`。

在`runner`内建立一个名为 proj.egret 的 Egret 项目。或者，将现有的 Egret 项目复制到这个位置。

建议：由于目前 Android 封装 Egret 项目还在发展阶段，不能保证所有能在浏览器正常运行的 Egret 项目都可以在封装打包后同样正常运行。因此建议用 egret create 建立的标准项目来学习本教程。
到这里，项目目录结构如图所示：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643863323337392e6a7067.jpg)

后续小节将在runner内建立与proj.egret并列的目录，对应Android封装项目，我们命名为app。

这样，我们设计总项目基本结构计划图：

    ├─runner
    │  ├─proj.egret
    │  └─app

-----

### 建立Android开发环境

本文的重点不在这里，并且网上有大量优质的教程来教你这个，所以不再详述，只给出一些要点提示。 官方推荐使用JetBeans公司的开发工具IntelliJ IDEA： [使用IntelliJ IDEA 13搭建Android集成开发环境（图文教程）](http://www.cnblogs.com/smyhvae/p/4013535.html)。
该公司两种Android开发工具对比：
a. Intellij IDEA。 Intellij IDEA 现在支持两种编译模式Ant(ADT的模式)和Gradle(Android Studio的模式)，所以IDEA直接无缝的导入Eclipse项目，再ADT停止更新后，可以完美的使用Gradle编译方式，所以更推荐Intellij IDEA。
b. Android Studio。因为Android Studio只使用新的Gradle模式，因为Gradle要求实时联网编译，暂时不太建议一般用户试用。
当然，您可以选择Eclipse或其他开发工具，但开发细节与教程不一样的地方，请自行解决~

为帮助习惯使用Eclipse开发者学习本教程相关内容，特推荐相关教程：

[Eclipse如何导入Android项目？](http://jingyan.baidu.com/article/020278116a018c1bcc9ce5a7.html)

-----

### 准备Egret项目的Android封装模板

由于从Egret项目纯手工建立App包含复杂的配置过程，因此Egret官方提供了现成的模板，方便我们快速建立App，Egret官方定义该模板名为Egret-Android-Support。

Egret打包Android App的项目依赖我们准备好的Egret标准项目，并且从Egret-Android-Support模板开始。所以进行Android封装前，我们要部署好该模板。

进入[官方Egret-Android-support](http://www.egret.com/downloads/android.html)下载最新版`Egret-Android-Support`，解压到一个相对稳定的路径。这样我们建立不同的Android App打包项目时，命令的模板参数都指向该路径即可。

比如，解压到路径`D:\_env\egret-android-support`。该路径内结构如图：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643863613835352e6a7067.jpg)

-----

建立Android封装项目

首先熟悉一下封装命令：

```
egret create_app app_name -f h5_game_path -t template_path
```

`app_name`参数即我们要创建的Android封装项目的目录名称，我们在前一节已经约定该名称为`app`。
`-f`参数用来指定标准的Egret项目路径。
`-t`参数用来指定创建Android封装项目的模板路径。直接使用上一节准备好的路径即可。

在`runner`目录下，使用该命令建立Android项目：

```
...\runner> egret create_app app -f proj.egret -t D:\_env\egret-android-support
```

>请注意，必须在项目的主目录路径执行该命令，本例中即为在runner目录内执行。
如果该命令执行过程有报错，通常是由于执行过程检查Egret项目中的文件结构不完整造成的。这种情况下，请对Egret项目执行一次编译或引擎编译，之后再次运行create_app命令即可。

正确运行`egret create_app`之后，则有如图目录结构：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643863626534372e6a7067.jpg)

注意runner\app\proj.android即为Android项目目录。接下来，我们便导入这个目录为Android项目，并打包运行。
从现在的目录结构可以看到，标准Egret项目的结构出现在了Android封装项目的assets/egret-game目录中。其内容与Egret标准项目内容是否完全一致我们并不需要清楚，因为引擎提供的同步命令会自动完成这些工作。后边介绍Egret项目同步时，会详细说明相关内容。

-----

### 在开发环境中导入Android封装项目及进行基本配置

由于历史遗留的问题，目前Android Support模板是Eclipse项目，但对于海纳百川的IntelliJ IDEA，这完全不是问题——IntelliJ可以完美导入Eclipse项目，并且可以选择同步Eclipse项目文件。

打开IntelliJ IDEA，首先导入项目：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643863643431362e6a7067.jpg)

在弹出的对话框中选择Android项目目录，如前一节所述，选择 runner\app\proj.android：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643863663635312e6a7067.jpg)

按OK，在紧接的`Import Project`步骤，在`Import project from external model`中选择`Eclipse`：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643864313736632e6a7067.jpg)

按Next，会提示`Select Eclipse projects directory`，默认路径通常是计算正确的，即我们在对话框第一步所选择的`runner\app\proj.android`目录。其他选项保留默认即可： 

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643864353737302e6a7067.jpg)

继续Next，提示Select Eclipse projects to import，确保勾选了app，底部也勾选Open Project Structure after import：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643864363834642e6a7067.jpg)

Next，该选择Android SDK了，选择准备好的Android SDK：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643864653361342e6a7067.jpg)

前面的建立 Android 开发环境小节准备好了再到这里继续。
通常应该选择最新的Android SDK，因为通常的SDK都是向下兼容的。如拿不定注意，就选择本教程所使用的4.2.2版本SDK。

到这里，导入结束，按下`Finish`。
依照之前的选项，弹出`Project Structure`对话框。
默认该对话框会自动选择`Modules`选项卡，确认在列表中选择`app`模块。

>IntelliJ新手提示：IntelliJ中的模块(Module)对应于Eclipse的项目，IntelliJ中的Project对应于Eclipse中的工作空间概念，这个区别不搞清楚很容易熬出浆糊！

模块右侧的子选项卡选择Dependencies，将IntelliJ所不需要的adt依赖删除：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643865383731632e6a7067.jpg)

删除后点`Apply`按钮生效。
然后我们还需要添加egret的Android支持库，在`Dependencies`标签页右侧点`+`选择`Library`:

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643865396237372e6a7067.jpg)


在弹出的小对话框中选择`egret`库：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643865636137622e6a7067.jpg)

`Add Selected`确认，之后`Dependencies`标签页内容应该是如图所示：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643865656632622e6a7067.jpg)

注：如果没找到egret库的话可以用可以按 Ctrl + Shift + Alt + S 打开项目设置菜单，或者直接点击：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303830392f353563366364663832646163352e706e67.png)

打开项目设置：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303830392f353563366365336262316261302e706e67.png)

选择 Libaries 选择上图的小加号，打开添加库的菜单，在如下图的路径内找到 egret 库，添加进来：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303830392f353563366366336134306561382e706e67.png)

最后，还要确认一下语言等级正确，左侧选择Project，在Project language level中选择6开头的选项：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643930316363392e6a7067.jpg)

按OK！至此，已经导入项目，并完成项目的基本配置过程。

----

### 调试运行Android封装项目

项目本身就绪了，接下，我们再进行简单的配置，就可以打包项目并在真机上测试运行了。
在工具栏选择模块的配置编辑：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643930353730652e6a7067.jpg)

选中`app`模块，在右侧的`General`选项卡下部`Target Device`中选择`USB device`，OK确认：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643931313535302e6a7067.jpg)

确保当前开发PC连接了Android手机，并在手机打开USB调试选项。如无真机，请在上述对话框选择Emulator，并确保Emulator正常运行。
在工具栏选择调试app：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643931353564352e6a7067.jpg)

如前边步骤都正确的话，在console中应该显示：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643932343931642e6a7067.jpg)

至此，已经走完Android封装Egret项目的整个流程。

但这只是开始，我们还需要根据项目开发需求进行各种开发，测试。

----

### Android层面开发流程

如console运行图所示，打包app的包名为默认的org.egret.java.app，这些都是标准Android项目的配置参数。很容易的，可以从AndroidManifest.xml中进行修改。
这只是一个简单的举例。开发者可以根据需求进行特定的修改，比如接入某种Android SDK，就必须在这里进行。
Android层面的开发流程比较容易，修改后，直接Rebuild项目即可更新。

----

### Egret层面开发流程

很显然，项目的核心逻辑还需要在Egret标准项目内进行。
那么一个最重要的问题来了：Egret项目修改后是否能自动同步到Android封装项目呢？答案是NO！我们还需要手工同步。不必担心这个，我们下一节将会介绍如何自动化该步骤。

Egret部分修改后，在主项目目录(`runner`)运行如下命令：

```
...\runner>egret build proj.egret --runtime native -e

第三方库共计耗时：9.152秒

native拷贝共计耗时：0.387秒
```

其中`build`后紧跟的是Egret标准项目名，接着后边跟的参数请不要做任何改动。参数的意思还是比较明显的，就是进行native引擎编译。

从命令提示也能更多理解该命令的作用，那就是编译后复制到native项目，也就是Android封装项目内。

在建立Android封装项目一节中，我们已经观察到Android封装项目内的`assets/egret-game`目录有与Egret标准项目相同的结构，我们称之为Egret同步目录。

当执行同步命令前后，如对项目有版本控制跟踪，会发现Egret同步目录中的内容已经发生变化。

这时，我们回到Android封装项目，运行/调试，即可运行项目的最新状态！

-----

### 项目同步高级技巧

基本流程建立起来之后，最让大家感到蛋疼的可能不是需要来回切换项目，而是每次切换到Android封装项目还需要先同步一下——很可能会忘记同步，而导致开发效率降低。

还好，强大的IntelliJ已经为我们准备好了工具，来将该步骤完全省略，严格来说是捆绑到Run/Debug过程。以下来具体说明做法。

在主项目目录runner内新建一个批处理文件sync.bat，编辑内容为前一节的同步命令行：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643932363835612e6a7067.jpg)

在IntelliJ打开Settings(File-->Settings)，在左栏的Tools下选择External Tools，在右栏顶部按+号，在打开的Create Tool对话框输入如图的内容： 

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643933346631362e6a7067.jpg)

>注意，其中的Name字段可以自己随意填，Program字段用了IntelliJ中的宏，这样使用模块相对路径的做法是为了使该工具可以在不同的Android封装Egret项目中均可用。

工具创建好了，然后我们捆绑到Android封装项目中，使其可以自动运行。

确保在IntelliJ打开了Android封装项目，打开app的运行/调试配置对话框。在右栏的General选项卡底部Before launch部分按+，选择Run External tool： 

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643934313235622e6a7067.jpg)

在弹出的小对话框选择我们刚刚创建的工具Sync Egret：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643934333164662e6a7067.jpg)

回到运行/调试配置对话框，将刚刚添加的Sync Egret工具移到最上边：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643934353939382e6a7067.jpg)

按OK，到此已经配置捆绑同步成功。再次Run/Debug项目，首先会在底部的RunTab显示我们刚刚配置的Sync Egret工具运行： 

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643934613165302e6a7067.jpg)

工具运行结束后会立即进入之前的Run/Debug流程。
如此，我们再无须手工同步，同步Egret在Run/Debug时将完全自动完成！

### 自动同步并不总是需要

自动同步命令的执行时间并非很短，有时可能会超过20秒，这有时候是一种负担。
当某一阶段的开发工作主要集中在Android部分时，也就是Egret部分长期没有变化，那就建议将该自动工具从`Before launch`中删除，以避免不必要的时间浪费。

----

发布Release版本

到目前为止，Android封装项目的assets/egret-game目录中，仍然是Egret项目开发状态的结构。其中还包含bin-debug这样明显是debug版才有的目录。
大家都熟悉，Egret标准项目发布运行是分为debug版和release版的。同样在Android封装项目release阶段，Egret同步目录中的内容也需要提供为release版。
同步release版也同样需要在主项目目录执行一条命令：

```
...\runner>egret publish proj.egret -compile --runtime native
```

注意同步Egret项目的命令只需要一条，即在debug阶段执行Egret层面开发流程一节所述的egret build ...命令，在release阶段执行本节所述的egret publish ...命令。
执行该条命令后，再注意assets/egret-game目录内的结构，即发现bin-debug、luancher、libs已经消失，取而代之的是新增一个zip文件：

![](687474703a2f2f7365646e2e65677265742e636f6d2f75656469746f722f32303135303532372f353536353830643934633139662e6a7067.jpg)

每次进行测试之前，建议先删除手机旧版本APP。

----

实例项目下载

项目全部源码：[下载地址](http://docs.egret.com/assets/packages/doc/win-android-package/Win-Android-Package.zip)