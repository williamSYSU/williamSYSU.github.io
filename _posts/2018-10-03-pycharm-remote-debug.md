---
layout:         post
title:          "使用Pycharm的远程调试"
subtitle:       "实时同步本机与服务器的代码，解放本机资源，高效利用服务器"
data:           2018-09-18
author:         "William"
header-img:     "img/post-bg-universe.jpg"
catalog:        true
tags:
    - Softwares
    - How to
---

很多时候需要编写在服务器上运行的代码，传统的方法有两种，一个是在本机码好后，传输到服务器上用命令行运行，另一个是只是直接在服务器上一边写代码一边debug。两种方法都有其各自的缺点，尽管还有很多方法来加速这个过程，但是总感觉缺少一体化的流程，无法做到无缝连接。

偶然发现可以通过Pycharm的服务器部署加远程调试功能来实现这个流程，可以让你像在本地运行代码一样，把代码放到服务器上运行，甚至可以说你很难分辨得出这到底是在本地运行还是在服务器上运行。话不多说，搞起来吧~

## 1. 下载Pycharm Professional

Pycharm社区版没有服务器部署(<code>Tools -> Deployment</code>)这个功能，需要到官网下载Pycharm的Professional版本才可使用。

如果学校有教育邮箱，可以用教育邮箱注册Jetbrains账号，免费使用一年，一年后可以再使员该邮箱续用。

### 注册教育版Jetbrains账号

- 前往[Jetbrans 官网](https://www.jetbrains.com/)，在页面顶部进入<code>Support -> Education</code>

![1538551580296](/img/image1.png)

- 点击<code>APPLY FOR FREE STUDENT PACK</code>

![1538551713859](/img/image2.png)

- 点击<code>APPLY NOW</code>

![1538551786068](/img/image3.png)

- 填写你的相关信息，在*Email address*中填写教育邮箱，教育邮箱的格式为*xxx@xx.edu.cn*，填写完后，点击<code>APPLY FOR FREE PRODUCTS</code>

![1538551929617](/img/image4.png)

- 最后登录你的教育邮箱，查看Jetbrains发给你的验证邮件，点击验证链接，进入创建账号界面。最后用你的教育邮箱创建Jetbrains账号，这样就可以用你的账号登录Pycharm Professional了。



## 2. 设置服务器部署

- 在Pycharm菜单栏中，点击<code>Tools -> Deployment -> Configuration</code>

![1538552338229](/img/image5.png)

- 点击左上角的添加按钮(“+”)，创建一个新的映射，输入连接的名字，类型选择*SFTP*。

![1538552528561](/img/image6.png)

- 填写相关信息，可以选择勾选保存密码。

![1538552785562](/img/image8.png)

- 点击<code>Test SFTP connection…</code>，测试连接。

![1538553070467](/img/image9.png)

- 设置<code>Root path</code>，这个根目录表示在Pycharm中浏览远程服务器时默认打开的根目录，这里可以设置为项目目录的上一级目录。假设当前在服务器中，项目文件所在位置为<code>/home/root/Documents/william/sentiment analysis</code>，则可以设置<code>Root path</code>为<code>/home/root/Documents/william</code>，当然<code>william</code>目录下还包含了其它项目。

![1538553561859](/img/image11.png)

- 设置<code>Mappings</code>映射关系。服务器部署的本质是将本地的一个文件夹与服务器的一个文件夹进行映射，保持这两个文件夹中的文件保持一致，注意这种一致是完全一致，而不包含版本信息。<code>Local path</code>设置为本地项目文件的根目录，即项目文件夹的名称。<code>Deployment path on server</code>则设置成服务器的项目文件夹名称。本地和服务器的项目文件夹名称可以不一样，Pycharm这是会将你设置的这两个文件夹进行映射，同步两个文件夹中的内容。

  > 注意：服务器文件夹的映射路径是<code>Root path</code>+<code>Deployment path on server</code>，所以前面设置的<code>Root path</code>是项目文件夹的上一级目录。

![1538553965610](/img/image12.png)

- 设置<code>Excluded Paths</code>，排除不需要上传的文件夹。有些文件夹我们并不想每次都上传到服务器，如数据集文件夹、.idea文件夹、.git文件夹等。这样我们在每次手动上传整个项目文件夹的时候，这些文件夹便不会再上传，如果数据集文件发生改动，需要再次上传到服务器，可以在<code>Tools -> Deployment -> Browse Remote Host</code>中查看服务器文件，并手动将本地的文件拖拽到服务器的相应文件夹中，即可上传到服务器中。

![1538554527550](/img/image13.png)

- 设置自动上传和手动上传。点击<code>Tools -> Deployment -> Automatic upload</code>，显示为√时，则表示当前为自动上传模式，再次点击则为手动上传模式。

  设置成自动上传时，每次改动代码，Pycharm都会自动上传当前改动的文件，实时更新服务器的对应文件。自动上传适用于代码仍在初期以及调试阶段，需要不断改动代码以及调试代码。当整个项目差不多定型，需要稳定在服务器中运行时，建议把上传设置为手动上传模式，这样不会影响服务器的程序运行。

![1538555037679](/img/image14.png)

- 如果是第一次在服务器运行，点击<code>Tools -> Deployment -> Upload to... -> XXX</code>，将项目文件上传到服务器。也可以在<code>Tools -> Deployment -> Browse Remote Host</code>中，手动将整个项目文件拖拽到对应目录中。



## 3. 设置远程调试

要将本地的项目映射到服务器上运行，需要设置服务器的远程解释器。其原理是用服务器的解释器来解释本地项目文件，并用服务器的解释器来运行项目，用ssh协议将服务器运行的结果以及过程返回到本地，实现在本地模拟服务器运行的情景。

- 打开设置<code>Settings -> Project Interpreter</code>
  - 添加新的解释器；
  - 选择<code>SSH Interpreter</code>；
  - 点击<code>Existing server configuration</code>；
  - 选择刚刚设置的服务器连接，点击下面的<code>Move</code>；
  - 点击<code>Next</code>

![1538555557753](/img/image15.png)

- 设置服务器的python解释器位置，并设置同步的文件夹

![1538555643944](/img/image16.png)

- 设置同步文件夹为服务器的项目文件夹名称，注意前面不要加<code>/</code>，服务器的路径是之前设置的<code>Remote path></code>加上这里设置的名称，点击<code>OK</code>。

![1538555764951](/img/image17.png)

- 设置完成后，可以在设置中看到服务器的解释器所安装的包，至此，关于Pycharm的远程调试则全部设置完成 了。



## 4. 使用服务器解释器运行程序

运行程序时，使用服务器的解释器运行，查看运行结果中是否使用了远程解释器。当运行结果的第一行出现<code>ssh://xxx</code>等字样，说明当前正在使用服务器的解释器运行，并且程序实际上是在服务器中运行，而不是在本机中运行。自此，就可以愉快地在服务器上玩耍啦~

![1538556066736](/img/image18.png)