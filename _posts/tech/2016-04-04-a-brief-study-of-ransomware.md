---
layout: post
title:  "恶意勒索软件初探"
category: [tech ]
tags: [ransomware,]
description: 简单分析和对比近期出现的恶意勒索软件
header-img: "img/blogs/blog-ransomware.jpg"
---



----

**新搭建了个人博客，文章全部转到[http://blog.datarepo.cn](http://blog.datarepo.cn)上更新。**

> 恶意勒索软件（[Ransomware](https://en.wikipedia.org/wiki/Ransomware)）通过加密、锁定等方式破坏用户对计算机软硬件系统或文件资源的访问，感染后的数据难以用现有的方法恢复，用户需要支付一定的赎金才能重新获得访问权。随着黑客技术的不断提高，恶意勒索软件已经成为一种难以追踪且能够获得大量非法收入的手段。近期已有多家医院和医疗服务机构受到勒索攻击，并带来了不小的经济损失。其他企业和个人也成为攻击对象，需要引起足够的重视。

## 恶意勒索软件一般实施流程 ##

1. **勒索软件传播**。利用特洛伊木马、僵尸网络、系统漏洞或社会工程学方法。常见传播方法为：发送垃圾邮件。邮件往往伪装为由合法公司发送，标题通常为发票、协议修改等，如：`ATTN: Invoice J-6xxxxxx`， `FW: Payment #xxxxx`。邮件附件为word文档，或者修改了后缀的Zip压缩文件（如修改为.pdf并且将文档的图标作相应修改），或者为JavaScript脚本。也有勒索软件在邮件中给出一个Dropbox链接，诱导用户下载。

2. **勒索软件触发**。若恶意软件包含在word文档附件中,打开word文档后，文档内容往往显示为乱码，文档标题提示开启宏查看内容，若开启了宏之后，则触发了软件或脚本的执行，在远程服务器中下载恶意软件执行代码等。若下载的附件为JavaScript脚本，或恶意软件包含在Zip包中，或者下载自Dropbox，则双击打开该文档即可能触发软件的执行。

3. **恶意代码执行**。勒索软件触发后，可能执行的恶意行为包括：
  * 修改系统注册表，添加恶意软件的开机自启动；
  * 删除系统还原点数据，禁止系统自动备份；
  * 利用系统漏洞，获取系统控制权并禁止用户访问；
  * 加密磁盘中用户文档数据，不仅本地磁盘，还包括共享网络磁盘；
  * 破坏系统启动记录和文件系统，使系统崩溃。

4. **迫使用户支付**。恶意行为执行完毕后，会在明显的位置告知用户的系统发生了什么，需要执行什么样的操作才能恢复。如将系统桌面改为写有提示的图片，将各个加密过的文件夹内放置用于提醒的图片或文本文档。提示内容一般为需要用户在期限内支付一定的金额才能恢复系统，若期限内不支付，则赎金增加且有可能永久的删除用于恢复系统的数据。  

5. **交还控制权限**。在用户支付赎金后，攻击者会使用技术手段交还对软硬件或文件系统的控制权。对于加密型的勒索软件，一般给用户下载一个定制的解密工具以及解密密钥，来恢复被加密的数据。

## 恶意软件常用的技术、手段和工具 ##

* **Social Engineering （社会工程学）**

  在计算机科学中，社会工程学是指通过与他人的合法交流，来使其心理受到影响，做出某些动作或者是透露一些机密信息的方式。在勒索软件的传播过程中，通过伪造的垃圾邮件，使得用户以为邮件内容为可信的或者重要的事件，从而下载并打开邮件附件，触发勒索软件的执行。

* **Packer、Crypter**

  软件加壳和加密是恶意软件通常采用的技术，能够辅助恶意软件躲避杀毒软件的查杀，并尽可能阻止恶意软件被安全人员逆向分析。

* **Word/Excel宏**

  为了提高工作效率，微软在起office软件中提供了宏功能，能够通过宏执行批处理命令，并可以通过用户自己编写VBA（Visual Basic for Applications）脚本增加灵活性。恶意软件的执行代码往往通过网络下载获得，在Word文档开启宏之后，会执行一段混淆的VBA脚本下载恶意代码，从而感染用户系统。

* **AES、RSA加密**

  一个通过精心设计的加密型恶意勒索软件，将用户的数据加密后的恢复问题转化为一个数学问题，即现代密码学中加密算法的破解问题。由于密码学的发展，先进的加密技术（如RSA-2048、AES-128）在不知道密钥（或只知道公钥）的前提下，采用现有工具和手段难以在可接受时间内破解。因此，现代密码的加密技术是加密型勒索软件得以实施的一个理论基础。

* **Bitcoin**

  比特币是一种全球通用的加密互联网货币。与采用中央服务器开发的第一代互联网不同，比特币采用点对点（P2P）网络开发的区域链。比特币不依靠特定货币机构发行，而是通过特定的算法大量计算产生。作为货币，包括中国在内的许多政府不承认其合法性，但在美国等国家承认其作为货币的合法地位。基于密码学的设计可以使比特币只能被真实的拥有者转移或支付，同时确保货币所有权与沟通交流的匿名性。因此为攻击这提供一种理想的支付途径，能够做到在收取到赎金的前提下不被追踪。

* **Tor Browser**

  Tor（The Onion Router, 洋葱路由器）是实现匿名通信的自由软件，用户通过Tor可以在因特网上进行匿名交流，实现匿名对外连接、匿名隐藏服务。Tor Browser是Tor项目的旗舰产品，能够自动通过Tor网络启动Tor的后台进程连接网络。一旦关闭程序便会自动删除隐私敏感数据，能够防范流量过滤和嗅探分析。这些特性使得其成为黑客与用户进行网络通信的不二之选，实现用户对攻击者服务器的访问而不被追踪。

* **Botnet**

  僵尸网络病毒Gameover ZeuS被美国FBI成为“史上最复杂、最具破坏性的网络僵尸病毒”，尽管在2014年反僵尸网络组织Operation Tovar已经中断了其运行,但危险依然存在。Gameover ZeuS不仅会利用ZeuS木马感染计算机，窃取在线电子邮件账户、社交网络和网银于其他在线金融服务，还会传播Cryptolocker，从而加密数据并索要赎金。


## 近期流行的恶意勒索软件 ##

近期恶意软件十分猖獗，出现了大量的恶意勒索软件变种，下面仅对部分进行罗列，并给出相关报道，如希望详细了解，可以自行网络检索。

1. **Locky**
  * McAfee （迈克菲）: [Locky Ransomware Arrives via Email Attachment](https://blogs.mcafee.com/mcafee-labs/locky-ransomware-arrives-via-email-attachment/) 2016.03.11
  * Avast （爱维士）: [A closer look at the Locky ransomware](https://blog.avast.com/a-closer-look-at-the-locky-ransomware) 2016.03.10
  * Avira （小红伞）: [Locky ransomware is dead, long live Locky](http://blog.avira.com/locky-ransomware-is-dead-long-live-locky) 2016.03.01

2. **CTB-Locker （Web服务器版本）**
  * Securelist （卡巴斯基实验室）: [CTB-Locker is back: the web server edition](https://securelist.com/blog/research/73989/ctb-locker-is-back-the-web-server-edition/) 2016.03.01

3. **Cerber**
  * Malwarebytes Labs: [Cerber Ransomware - New, But Mature](https://blog.malwarebytes.org/threat-analysis/2016/03/cerber-ransomware-new-but-mature/) 2016.03.11

4. **Petya （破坏文件系统的主引导分区MBR和主文件表MFT）**
  * G Data : [Randomeware Petya - a technical review](https://blog.gdatasoftware.com/2016/03/28226-ransomware-petya-a-technical-review) 2016.03.31
  * Bleeping Computer: [Petya Ransomware skips the Files and Encrypts your Hard Drive Instead](http://www.bleepingcomputer.com/news/security/petya-ransomware-skips-the-files-and-encrypts-your-hard-drive-instead/) 2016.03.25

5. **LeChiffre**
  * Malwarebytes Labs: [LeChiffre, Ransomware Ran Manually](https://blog.malwarebytes.org/threat-analysis/2016/01/lechiffre-a-manually-run-ransomware/) 2016.01.22
  * McAfee （迈克菲）: [McAfee Labs Unlocks LeChiffre Ransomware](https://blogs.mcafee.com/mcafee-labs/mcafee-labs-unlocks-lechiffre-ransomware/) 2016.03.28

6. **Maktub Locker （美丽又危险的勒索软件）**
  * Malwarebytes Labs: [Maktub Locker - Beautiful And Dangerous ](https://blog.malwarebytes.org/threat-analysis/2016/03/maktub-locker-beautiful-and-dangerous/) 2016.03.24

7. **TeslaCrypt**
  * Fortinet: [Nemucod Adds Ransomware Routine](http://blog.fortinet.com/post/nemucod-adds-ransomware-routine) 2016.03.16

8. **Criakl**
  * Phishme: [Ransomware Rising – Criakl, OSX, and others – PhishMe Tracks Down Hackers, Identifies Them and Provides Timeline of Internet Activities](http://phishme.com/ransomware-rising-criakl-osx-others/) 2016.03.10

## 防御方法 ##

### 通过技术手段 ###


近期，法国[Lexsi](www.lexsi.com)的研究人员研究了使用疫苗（vaccine）对抗Locky的方法。（由于恶意软件频繁出现变种，下列方法仅作为参考）

  * [A new dynamic vaccine against Locky](https://www.lexsi.com/securityhub/a-new-dynamic-vaccine-against-locky/?lang=en) 2016.04.01
  * [Abusing bugs in the Locky ransomware to create a vaccine](https://www.lexsi.com/securityhub/abusing-bugs-in-the-locky-ransomware-to-create-a-vaccine/?lang=en) 2016.03.22

采用的方法主要为：

  1. 将语言设置为俄语（部分恶意勒索软件对语言为俄语的系统不做处理），该方法对很多人不适用；

  2. 手动创建注册表值`HKEY_CURRENT_USER\Software\Locky`，并设置ACLs保护，阻止所有用户修改。该方法有效的原因为，在检查完语言后，Locky尝试创建`HKEY_CURRENT_USER\Software\Locky`，当创建失败时，Locky会立刻终止；

  3. 设置注册表id和completed值，欺骗Locky认为系统已经加密完毕。

  4. 破坏注册表中RSA pubkey，使得Locky重命名文档而不加密；

  5. 使用已知RSA密钥，由于Locky在加密前未对pubkey进行验证，因此可以设置已知的RSA公钥，即使Locky将文件加密，也可通过已知的私钥进行解密。

*这里只做简单介绍，具体方法参考文章 [Abusing bugs in the Locky ransomware to create a vaccine](https://www.lexsi.com/securityhub/abusing-bugs-in-the-locky-ransomware-to-create-a-vaccine/?lang=en)。*



### 增强安全意识 ###

增强安全意识对于保护系统免受恶意软件攻击具有至关重要的意义，主要的方法有：

1. 定期备份重要文件，文件备份后要与主机断开；

2. 对可疑邮件进行确认，确保为合法邮件后再打开邮件，下载附件；

3. 不随意打开网络链接，防止下载恶意软件；

4. 默认关闭office的宏，不要在浏览邮件附件文档是打开它；

5. 安装先进的防病毒软件（建议使用国外的知名软件），并定期更新和扫描；

6. 对使用的操作系统和应用软件及时打好补丁。

### 针对企业用户 ###
老牌代码安全审计机构[NCC Group](https://www.nccgroup.trust) 于2016年3月11日针对企业发布了[Ransomware: what orgnisations can do to survive](https://www.nccgroup.trust/uk/our-research/ransomware-what-organisations-can-do-to-survive/)，帮助企业如何能够最大限度的减少初次感染的可能性。
