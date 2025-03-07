# 使用Clion配置Ardunio开发环境


&lt;!--more--&gt;

### 安装Python环境

官网下载https://www.python.org/downloads/ 安装包并进行安装。

验证Python是否安装成功

![image-20240508143558733](./assets/image-20240508143558733-1741313878942-1-1741333992703-1.png)

### 安装PlatFormIO(core)

点击下载脚本

[get-platformio.py](https://raw.githubusercontent.com/platformio/platformio-core-installer/master/get-platformio.py)

下载脚本后，在Cmd命令行里执行

```bash
python get-platformio.py
```

![image-20240508144027116](./assets/image-20240508144027116-1741313878942-2-1741333992703-2.png)

等待安装下载完成

![image-20240508145853120](./assets/image-20240508145853120-1741313878942-3-1741333992703-3.png)



### PlatformIO安装验证

1. 设置`C:\Users\Gavin\.platformio\penv\Scripts`文件夹到环境变量Path中

   ![image-20240509095754152](./assets/image-20240509095754152-1741313878942-4-1741333992703-4.png)

   &gt; tips
   &gt;
   &gt; ​	如果添加后在第二部验证无效果，请删除path环境变量重新添加即可。

2. 输入pio验证platform安装是否成功

![image-20240508150054822](./assets/image-20240508150054822-1741313878942-5-1741333992703-5.png)



### CLion插件配置

1. Market搜索PlatformIO for Clion并安装，安装完成后重启CLion

   ![image-20240508150211733](./assets/image-20240508150211733-1741313878942-6-1741333992703-6.png)

2. 新建项目--&gt;PlatformIO--&gt;输入 `arduino uno`

   ![image-20240508150250014](./assets/image-20240508150250014-1741313878942-7-1741333992703-8.png)

3. 创建项目

   ![image-20240508150418979](./assets/image-20240508150418979-1741313878942-8-1741333992703-7.png)

4. 等待platformIO-core创建的项目初始化完毕

   ![image-20240509100718018](./assets/image-20240509100718018-1741313878942-9-1741333992703-9.png)

5. 验证是否按照成功

   1. 接上Ardunio Uno开发板

   2. 随意编写一段程序通过CLion上传，观察控制台输出。

      ![image-20240509101109935](./assets/image-20240509101109935-1741313878942-10-1741333992703-11.png)

      点击上方的绿色小三角，等待编译烧录完成。![image-20240509101137448](./assets/image-20240509101137448-1741313878942-11-1741333992703-10.png)

   3. 查看输出结果

      ![image-20240509101556032](./assets/image-20240509101556032-1741313878942-12-1741333992703-12.png)

​	OK，上述就是如何使用CLion配置Ardunio开发环境。

​	End


---

> Author: [w2422](https://www.gvnote.com)  
> URL: https://www.gvnote.com/posts/platform-thrid-ide-installation/  

