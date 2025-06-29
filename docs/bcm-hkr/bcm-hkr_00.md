# 序言

*成为黑客* 将教你如何以攻击者的心态进行网络渗透测试。虽然测试网页应用的性能是常见的，但不断变化的威胁格局使得安全测试对防守方而言更具挑战。

有许多网页应用工具声称能够提供对潜在威胁的完整调查和防御，但它们必须根据每个网页应用或服务的安全需求进行分析。我们必须了解攻击者如何接近网页应用以及突破其防御的影响。

在本书的第一部分，Adrian Pruteanu 带领你走过常见的漏洞，并展示如何利用它们达到目标。书的后半部分则转换视角，将新学到的技术付诸实践，讲解在目标可能是流行的内容管理系统或容器化应用及其网络的场景下的攻击方法。

*成为黑客* 是一本从攻击者角度讲解网页应用安全的清晰指南，双方都能从中受益。

# 本书的读者对象

读者应该具备基本的安全经验，例如通过运行网络或在应用开发过程中遇到安全问题。正规安全教育有帮助，但并非必要。本书适合至少有两年开发、网络管理或 DevOps 经验的人，或对安全有浓厚兴趣的人。

# 本书的内容

第一章, *攻击网页应用入门*，介绍了我们在渗透测试过程中必须遵循的工具、环境和基本的 ROE（规则）。我们还会看看渗透测试者的工具箱，并探讨云作为网页渗透测试者的新兴工具。

第二章, *高效发现*，带你走一段提升信息收集效率的旅程，专注于如何提高对目标的情报收集效率。

第三章, *低悬果实*，阐明、强调并利用了一个事实：对于防守者而言，要始终正确地进行安全防护是非常困难的，许多简单的漏洞经常会被忽视。

第四章, *高级暴力破解*，详细讨论了暴力破解，并探索了几种在进行暴力攻击时如何保持低调的技巧。

第五章, *文件包含攻击*，帮助你探索文件包含漏洞。我们还将研究几种方法，利用应用程序底层的文件系统为我们所用。

第六章, *非带外利用*，讨论了非带外发现、应用漏洞的利用，以及在云环境中设置命令与控制基础设施。

第七章, *自动化测试*，帮助你自动化漏洞利用，包括使用 Burp 的 Collaborator 功能简化非带外发现过程。

第八章, *不良序列化*，详细讨论了反序列化攻击。我们将深入探讨这种漏洞类型，并研究实际的攻击方法。

第九章, *实用客户端攻击*，涵盖了与客户端攻击相关的信息。我们会讨论三种类型的 XSS：反射型、存储型和 DOM 型，另外还涉及 CSRF，并将这些攻击结合起来进行分析。同时，我们还会介绍 SOP 以及它如何影响加载第三方内容或攻击代码到页面上。

第十章, *实用服务器端攻击*，带你通过 XML 攻击服务器，并利用 SSRF 来链接攻击，从而进一步渗透网络。

第十一章, *攻击 API*，专注于 API 的测试与攻击。到目前为止你所学到的所有技能都将在本章中派上用场。

一台运行 Kali 或你选择的渗透测试发行版的虚拟机或主机将帮助你快速启动，尝试书中提到的一些场景。

第十三章, *容器安全破解*，帮助你了解如何在部署之前安全地配置 Docker 容器，并以一个被攻破的容器化 CMS 为例，展示如何导致另一个容器漏洞，从而导致主机完全被攻破。

# 为了最大限度地从本书中获益

+   你应该具备操作系统的基础知识，包括 Windows 和 Linux。我们将在本书中大量使用 Linux 工具和 Shell 环境，因此对该环境的熟悉程度非常理想。

+   一些脚本知识肯定会有所帮助，但不是必需的。Python、JavaScript 和一些 PHP 代码将在本书中出现。

+   我们将探索云中的命令与控制服务器，并强烈建议你在主要服务提供商上创建一个免费账户，以便跟随本书中的示例进行操作。

+   第十二章, *攻击 CMS*，探讨了如何攻击 CMS 并深入分析其漏洞。

+   我们常常从 GitHub 上的开源项目下载代码，虽然深入了解 Git 肯定会有所帮助，但它并不是必需的。

## 下载示例代码文件

你可以通过 [`www.packt.com`](http://www.packt.com) 账户下载本书的示例代码文件。如果你在其他地方购买了本书，你可以访问 [`www.packt.com/support`](http://www.packt.com/support) 注册，直接将文件通过电子邮件发送给你。

你可以按照以下步骤下载代码文件：

1.  请在 [`www.packt.com`](http://www.packt.com) 登录或注册。

1.  选择**支持**选项卡。

1.  点击**代码下载和勘误表**。

1.  在**搜索**框中输入书名，并按照屏幕上的说明操作。

文件下载后，请确保使用最新版本的以下工具解压或提取文件：

+   适用于 Windows 的 WinRAR / 7-Zip

+   适用于 Mac 的 Zipeg / iZip / UnRarX

+   适用于 Linux 的 7-Zip / PeaZip

本书的代码包也托管在 GitHub 上，网址为 [`github.com/PacktPublishing/Becoming-the-Hacker`](https://github.com/PacktPublishing/Becoming-the-Hacker)。如果代码有更新，它将被更新到现有的 GitHub 仓库中。

我们还提供了来自丰富书籍和视频目录的其他代码包，地址为 [`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)。快去看看吧！

## 下载彩色图片

我们还提供了一份包含书中截图/图表的彩色图像的 PDF 文件。你可以在此下载：[`www.packtpub.com/sites/default/files/downloads/9781788627962_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/9781788627962_ColorImages.pdf)。

## 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词汇、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟网址、用户输入和 Twitter 账户名。例如：“将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。”

代码块将以以下格式设置：

```
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

当我们希望引起你对代码块中特定部分的注意时，相关行或项会以粗体显示：

```
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
**exten => s,102,Voicemail(b100)**
exten => i,1,Voicemail(s0)
```

所有命令行输入或输出都将以以下格式显示：

```
# cp /usr/src/asterisk-addons/configs/cdr_mysql.conf.sample
     /etc/asterisk/cdr_mysql.conf
```

**粗体**：表示一个新术语、一个重要单词，或者是你在屏幕上看到的词语，例如菜单或对话框中的词语，也以这种方式出现在文本中。例如：“从**管理**面板中选择**系统信息**。”

### 注释

警告或重要提示将以这种方式显示。

### 提示

提示和技巧将以这种方式显示。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：如果你对本书的任何方面有疑问，请在邮件主题中提及书名，并通过`customercare@packtpub.com`与我们联系。

**勘误**：尽管我们已经尽力确保内容的准确性，但错误难免。如果您在本书中发现了错误，我们将非常感激您向我们报告。请访问，[`www.packt.com/submit-errata`](http://www.packt.com/submit-errata)，选择您的书籍，点击“勘误提交表格”链接，并填写相关信息。

**盗版**：如果您在互联网上发现任何我们作品的非法复制版本，请提供该地址或网站名称，我们将非常感激。请通过`copyright@packt.com`与我们联系，并附上相关材料的链接。

**如果您有意成为作者**：如果您在某个领域具有专业知识并且有兴趣撰写或参与撰写书籍，请访问 [`authors.packtpub.com`](http://authors.packtpub.com)。

## 评价

请留下您的评价。在您阅读并使用本书后，为什么不在您购买本书的网站上留下一个评价呢？潜在的读者可以看到并参考您客观的意见来做出购买决策，我们在 Packt 也可以了解您对我们产品的看法，而我们的作者也可以看到您对他们书籍的反馈。谢谢！

如需了解更多关于 Packt 的信息，请访问 [packt.com](http://packt.com)。
