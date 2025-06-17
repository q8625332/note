
## 哪里版权不明？？你倒是指出来啊
## idea必装插件-个人笔记

 **Coding Easier** 
 

 - 谷歌中英互译（根据选中的内容自动辨别）alt shift Q
 - 翻译及便捷替换(中英互译，多词备选，方便快捷) alt shift A
 - 快捷的创建一个常量 alt shift Z
 - json格式化、压缩、转义、去转义alt shift J
 - 字符串相关操作 为了方便见名识意，这里命名基本都按照转化后的格式命名 all case 显示所有支持的格式 alt shift W 
 	to CONSTANT 转化成常量
	to camelCase 转化为小驼峰
	to underscore_case 转化为下划线命名
	to PascalCase 转化为大驼峰
	to Kebab-case 转化为短横线命名(首字母大写)
	to kebab-case 转化为短横线命名(首字母小写)
	to words 拆分驼峰、下划线、短横线、常量等正常命名的变量
	to UpperCase 转化为全大写形式
	to LowerCase 转化为全小写形式

|  功能	| win快捷键 |mac快捷键|
|--|--|--|
| 中英互译 | Alt + Shift + Q |⌥ + ⇧ + Q|
|--|--|--|
| 翻译及便捷替换 | Alt + Shift + A |⌥ + ⇧ + A|
|--|--|--|
| 创建一个常量 | Alt + Shift + Z |⌥ + ⇧ + Z|
|--|--|--|
| json格式化、压缩、转义、去转义 | Alt + Shift + J |⌥ + ⇧ + J|
|--|--|--|
| 字符串相关操作 | Alt + Shift + W |⌥ + ⇧ + W|

[Coding Easier-官方文档](https://gitee.com/Chave-Z/CodingEasier)

**Translation**

> 翻译（带语音）
> 字符串相关操作

		
**Chinese(Simplified)**

> idea 中文显示（官方推出的中文包）

**CodeGlance**

> 迷你视图
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210319164422842.png)

**GenerateAllSetter**
> 可以快速的生成get set的方法
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210319165637726.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

**JRebel**

> 这个就牛逼了
> 真正的热部署
> JRebel启动项目 ctrl + F9 或者 ctrl + Shift + F9 可以快速的将修改后的代码加载到jvm里面 不需要重新启动项目
> 不过这个软件是付费的（需要破解）

安装JRebel
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210319170232540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
激活
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021031917041076.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
2019-04-01 愚人节最新激活地址 : 

```java
http://jrebel.cicoding.cn/4571ab86-eb0d-4d2b-999e-37406bb8ba38
```
如果出现激活过期的情况下 , 可以重新生成一下GUID , 替换原来的GUID即可 .
[生成GUID地址](https://www.guidgen.com/)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210319170500217.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210319170507752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
方法2：
方法2与方法1类似，只不过最新版的2022.4.2 这个版本的jrebel应该是更改了激活方式 qekang方式激活不成功。
1.前置步骤均一致，在填入Team URL时，填入以下内容
```java
http://127.0.0.1:8888/{GUID}

GUID可以使用在线GUID地址在线生成，然后替换{GUID}就行。
```
2.下面邮箱地址可随便输入。

3.选择我同意

4.提交

需要在[此处下载](https://github.com/ilanyu/ReverseProxy/releases/tag/v1.4)自己机器系统相对应的工具，如图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/7acfd3f30b544949a78976302be51758.png)
下载好了后，进行安装打开（激活插件时，程序保持启动，激活成功后可关闭），如图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/0a1d66bc50e747ae8f14d534f659fd60.png)
再次执行上面的操作步骤即可。


1.前置步骤均一致，在填入Team URL时，填入以下内容

正常完成第一步之后 , jrebel就能正常使用了 . 但很多人往往用不到几天就提示激活失败, 无法使用了 . 

jrebel激活之后默认是联网使用的 , 在该模式下 , jrebel会一直联网监测激活信息 . 所以要调为离线使用的

操作方法就是点击Work offile 按钮即可 . 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210319170521680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
**JRebel mybatisPlus**

> 配合mybatisPlus 使用

**Lombok**

> 一个注解 @data 给实体类 字段生成get set 等 方法

**MybatisPlus**

[使用详情](https://github.com/kana112233/mybatis-plus-plugin/wiki)

**Properties to YAML Converter**

> 将springboot 的配置文件转成 yml 文件

 **Easy Javadoc**
 
> 能帮助java开发者自动生成javadoc文档注释
> 将光标放置到想要生成注释的类、方法或者属性上，然后按下快捷键ctrl \或者command \，即可生成注释，你的方法名起的越贴切，注释越得体
> 将光标放置到想要生成注释的类上，然后按下快捷键ctrl shift \或者command shift \，即可批量生成文档注释
> 光标选中要翻译的中文，然后按下快捷键ctrl \或者command \，即可自动变成英文，类似程序员起名神器。
> 光标选中要翻译的非中文，然后按下快捷键ctrl \或者command \，即弹出选中的翻译结果，再也不用在词典和IDEA之间来回切换了。

[Easy Javadoc-文档地址](https://gitee.com/starcwang/easy_javadoc#%E4%BB%8B%E7%BB%8D)

## RestfulToolkit: 快速定位controller层接口、接口测试
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415153408815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## 安装Material Theme UI
![在这里插入图片描述](https://img-blog.csdnimg.cn/60046e11d83c4ecdadb834f716723e55.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/16963b1c4a5545a78c071ddbb2c31e46.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)
**这个设置去掉**
![在这里插入图片描述](https://img-blog.csdnimg.cn/8f270ec2d0c54b2ebc865c7b05265d15.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)

## 安装主题
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517094946117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

这款主题也不错
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517095005227.png)

**安装主题的icon**

> 原本的icon特别少
> 安装一些好看的icon
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517100214599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
**icon的一些设置**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517100447882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
**再给它来个背景图**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517101825988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

> 本人选了个4k图片
> 配置下透明度
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517102023292.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

> 完美的展示了idea的美化程度
> 最终效果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517102106592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

> 如果注释比较不明显

设置这三个
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518161632939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)	
效果显示
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021051816165115.png)


## 主题 Vuesion Theme

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/ec695be2dfb6cdc27d904c96a5f56ec5.png#pic_center)

## Hiberbee Theme
> 主题小黄蜂

![[Pasted image 20250617142706.png]]


## 安装代码的括号区分

> 这个可以明显的帮我们实现括号的区分
> Rainbow Brackets 收费了，使用就安装开源的Rainbow Brackets Lite - Free and OpenSource

![[Pasted image 20250617143001.png]]
> 然后在安装变量改色
>  Rainbow Variable

![[Pasted image 20250617143116.png]]
**显示效果**


## GsonFormatlPlus

> 将json直接转成java实体

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210521183943785.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021052118432344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210521184341653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## java Bean to Json

> 将实体类转成json

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210521184052980.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021052118440230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

数据会直接覆盖到剪切板

## Codota 代码自动补全
当你对某个变量进行赋值的时候，Codota便会根据它学习到代码知识，给出相应的建议，使用的比例，如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a272a64c545c4329bb83689ec1d02f4d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCY5Y-26aOO5YeM,size_20,color_FFFFFF,t_70,g_se,x_16)

##  AceJump

> 在我们写代码的时候，双手都在键盘上敲击，突然动用鼠标，就感觉很异样的感觉，有没有不动用鼠标的方法呢，有AceJump就帮我们可以解决动用鼠标的问题。
> 效果如下

当我们的鼠标在如下图的位置：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071509321383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
按下ctrl + ； 
会出现蓝色方块（我电脑是蓝色方块，你们可能是其他的颜色），然后输入想要定位的代码，再根据前面的字母就能直接定位鼠标的位置了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715093338680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

## MyBatisX（使用mybatis plus 建议用这个）

> 程序员的友好工具

效果图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/5c10fd449e7b4dc0b52f816c4e4730a5.png)
## MOMO CODE SEC INSPECTOR

> 本插件作为Java项目静态代码安全审计工具，侧重于在编码过程中发现项目潜在的安全风险，并提供一键修复能力。
> 
> 本插件利用IDEA原生Inspection机制检查项目，自动检查当前活跃窗口的活跃文件，检查速度快，占用资源少。
> 插件提供的规则名称均以"Momo"开头。

[效果：](https://github.com/momosecurity/momo-code-sec-inspector-java)

## chapter_reader
**在线&本地阅读插件**

> idea没有工具栏的菜单了
> 所以按两下shift，弹出菜单搜索read，进行配置

![在这里插入图片描述](https://img-blog.csdnimg.cn/84c157f44fd847a0bf0fbe054474ae8d.png)
**配置小说目录网址**
![在这里插入图片描述](https://img-blog.csdnimg.cn/992dd7b20207488285911851bdf3fa3a.png)
**选择要看的章节**
![在这里插入图片描述](https://img-blog.csdnimg.cn/4b70e4a838594046bceb518e951836e6.png)


**使用效果**
![在这里插入图片描述](https://img-blog.csdnimg.cn/691f70d134b1447db0121ffdf146b4ad.png)
## 再换个卡哇伊的加载进度条 Nyan Progress Bar

> 装上Nyan Progress Bar之后，进度条也变得活泼起来，多加载一会儿也无妨。

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/f81218bb6b1dcbc83f32b3c99b8d36e8.png#pic_center)
## 烟花为我绽放 active-power-mode-x
![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/d8229fc87620b7f2f89ee758b78f9a80.gif#pic_center)
## Mario Progress Bar
![在这里插入图片描述](https://img-blog.csdnimg.cn/cbe46eb95b2d46b49dd2b894a6af4d12.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/7169793f1b824796831e747787927fc0.png)
## Sticky Scroll

> 可以实时看到滚动的方法
> 效果如下

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4a00a043abdc4a608073aab02cebacc2.png)


## MyBatis Log Free

>插件主要提供能力：
>将mybatis xml转成真实SQL语句

sql控制台中点击「sql」图标生成SQL

![[Pasted image 20250604091756.png]]