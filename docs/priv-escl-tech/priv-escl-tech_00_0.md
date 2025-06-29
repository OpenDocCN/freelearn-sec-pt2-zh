# 前言

本书是一本关于 Windows 和 Linux 系统特权提升过程的全面指南，旨在通过提供真实的练习和场景，采用易于操作的方式让读者掌握实用技能。本书从介绍特权提升开始，涵盖了搭建实操虚拟黑客实验室的过程，这个实验室将用于展示本书中所讨论的各种技术的实际应用。每一章内容都基于前一章展开，通过提供可以复制的练习和场景来验证学习过程。

你将学习如何从目标系统中枚举尽可能多的信息，利用手动和自动化枚举工具，通过多种技术（如冒充攻击或内核漏洞利用等）提升 Windows 系统的特权，并通过使用内核漏洞或利用 SUID 二进制文件提升 Linux 系统的特权。

本书分为三个部分，互相衔接，第一部分涵盖特权提升的简介、如何在目标系统上获得初步立足点，以及如何从目标系统中枚举信息。接下来的两部分专注于介绍适用于 Windows 和 Linux 系统的各种特权提升技术和工具。

本书将为你提供必要的技能，使你能够从目标系统中枚举信息，识别潜在漏洞，并利用手动技术或自动化工具提升目标系统的特权。

# 本书适合谁阅读

本书面向学生、网络安全专业人员、爱好者、安全工程师、渗透测试人员，或者任何对渗透测试或信息安全有浓厚兴趣的人群。本书可以作为个人、公司或培训机构的学习材料。

无论你是信息技术行业的新手学生，还是经验丰富的专业人士，本书都能为你提供有价值的信息，帮助你提升渗透测试技能。

# 本书内容

*第一章*，*特权提升简介*，介绍了特权提升过程、各种特权提升攻击类型，以及 Windows 和 Linux 系统上特权提升的差异。

*第二章*，*搭建实验环境*，介绍了虚拟化的概念，如何搭建自己的渗透测试实验室，如何设置易受攻击的虚拟机，以及如何安装和配置 Kali Linux。

*第三章*，*获取访问权限（漏洞利用）*，重点讲解了设置 Metasploit 框架、使用 Nmap 进行信息收集、识别漏洞以及利用这些漏洞获取系统访问权限的过程。

*第四章*，*执行本地枚举*，涵盖了手动和自动从 Windows 和 Linux 系统中枚举信息的过程。

*第五章*，*Windows 内核漏洞利用*，探讨了通过 Metasploit 手动和自动执行内核漏洞利用，以提升权限的过程。

*第六章*，*伪装攻击*，解释了 Windows 访问令牌的工作原理，概述了枚举权限的过程，讲解了令牌伪装攻击，并涵盖了通过腐烂土豆攻击提升权限的过程。

*第七章*，*Windows 密码挖掘*，探讨了在文件和 Windows 配置文件中搜索密码、寻找应用程序密码、转储 Windows 哈希值并破解转储的密码哈希值，以提升权限的过程。

*第八章*，*利用服务*，涵盖了利用未引用的服务路径、利用二级登录句柄、利用弱服务权限和执行 DLL 劫持的过程。

*第九章*，*通过 Windows 注册表提升权限*，研究了利用弱注册表权限、自动运行程序和利用“始终以管理员身份安装”功能的过程。

*第十章*，*Linux 内核漏洞利用*，解释了 Linux 内核的工作原理，并涵盖了通过 Metasploit 手动和自动执行内核漏洞利用的过程。

*第十一章*，*Linux 密码挖掘*，重点讲解了从内存中提取密码、在配置文件中查找密码以及在 Linux 历史文件中查找密码的过程。

*第十二章*，*定时任务*，介绍了 Linux 中的 cron 作业，并涵盖了通过利用 cron 路径、cron 通配符和 cron 文件覆盖来提升权限的过程。

*第十三章*，*利用 SUID 二进制文件*，概述了 Linux 中文件系统权限的工作原理，并探讨了寻找 SUID 二进制文件以及通过共享对象注入提升权限的过程。

# 为了充分利用本书

要最大限度地利用本书，你应当具备基本的网络知识，特别是 TCP/IP、UDP 以及它们各自的协议。此外，考虑到本书所涉及技术的性质，你还应对 Windows 和 Linux 的工作原理和功能有所了解。

本书所需的硬件配置相对标准。你可以使用一台支持虚拟化并能够运行 Oracle VirtualBox 的笔记本或台式计算机。根据硬件和操作系统的规格，推荐以下配置：

+   **操作系统**（**Windows**）：Windows 7 或更高版本，最好是 64 位

+   **操作系统**（**Linux**）：Ubuntu、Debian、Fedora 或任何基于 Debian 或 Fedora 的稳定发行版

+   **处理器**：Intel i5 或更高

+   `RAM`：8 GB 或更高

+   **硬盘**：500 GB 硬盘

**如果你使用的是本书的数字版，我们建议你自己输入代码或从本书的 GitHub 仓库访问代码（下一节中提供了链接）。这样可以帮助你避免复制和粘贴代码时可能出现的错误。**

# 代码实战

本书的《代码实战》视频可以在[`bit.ly/3CPN0DU`](https://bit.ly/3CPN0DU)查看。

# 下载彩色图片

我们还提供了一份 PDF 文件，其中包含了本书中使用的截图和图表的彩色版本。你可以在此下载：`static.packt-cdn.com/downloads/9781801078870_ColorImages.pdf`。

# 使用的约定

本书中使用了一些文本约定。

**文本中的代码**：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名。例如：“在将 Bash 脚本下载到我们的 Kali 虚拟机后，我们需要将`linpeas.sh`文件传输到目标虚拟机。”

一段代码如下所示：

#include <stdio.h>

#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {

system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");

}

当我们希望引起你注意代码块中特定部分时，相关行或项目会以粗体显示：

#include <stdio.h>

#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {

system(**"cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p"**);

}

所有命令行输入或输出以如下格式书写：

ls -al /home/user/

**粗体**：表示一个新术语、一个重要单词或你在屏幕上看到的单词。例如，菜单或对话框中的单词会显示为**粗体**。例如：“从**管理**面板中选择**系统信息**。”

提示或重要说明

如此显示。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：如果您对本书的任何内容有疑问，请通过电子邮件 customercare@packtpub.com 与我们联系，并在邮件主题中注明书名。

**勘误**：尽管我们已尽力确保内容的准确性，但错误仍然可能发生。如果您在本书中发现了错误，我们将非常感激您向我们报告。请访问 [www.packtpub.com/support/errata](http://www.packtpub.com/support/errata) 并填写表格。

**盗版**：如果您在互联网上遇到我们作品的任何非法复制形式，我们将非常感激您能提供相关的地址或网站名称。请通过电子邮件 copyright@packt.com 与我们联系，并附上相关材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域拥有专长，并且有意撰写或参与编写书籍，请访问 [authors.packtpub.com](http://authors.packtpub.com)。

# 分享您的想法

阅读完 *特权提升技术* 后，我们非常希望听到您的想法！请 [点击这里直接访问亚马逊评论页面](https://packt.link/r/1801078874) 为本书留下反馈。

您的评论对我们和技术社区非常重要，将帮助我们确保提供优质内容。
