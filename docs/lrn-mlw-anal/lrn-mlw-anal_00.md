# 前言

计算机和互联网技术的进步改变了我们的生活，并且彻底改革了组织进行商业活动的方式。然而，技术的演变和数字化也带来了网络犯罪活动。对关键基础设施、数据中心以及私营/公共部门、国防、能源、政府和金融领域的网络攻击威胁日益增加，这给从个人到大公司各方都带来了独特的挑战。这些网络攻击利用恶意软件（也称为恶意程序）进行财务盗窃、间谍活动、破坏、知识产权盗窃和政治动机。

随着对手变得更加复杂并进行先进的恶意软件攻击，检测和响应此类入侵对于网络安全专业人员至关重要。恶意软件分析已成为应对高级恶意软件和定向攻击的必备技能。恶意软件分析需要对多种不同技能和学科有均衡的知识。换句话说，学习恶意软件分析需要时间并且需要耐心。

本书教授了使用恶意软件分析来理解 Windows 恶意软件行为和特征的概念、工具和技术。本书首先介绍了恶意软件分析的基本概念，然后逐步深入到更高级的代码分析和内存取证概念。为了帮助你更好地理解这些概念，书中通过实际的恶意软件样本、感染的内存镜像和可视化图表来展示示例。此外，还提供了足够的信息来帮助你理解所需的概念，并且在可能的情况下，提供了额外资源的参考，以供进一步阅读。

如果你是恶意软件分析领域的新手，本书应该能帮助你入门；如果你在该领域已有经验，本书将进一步提升你的知识。无论你是为了进行取证调查、响应事件，还是为了兴趣而学习恶意软件分析，本书都能帮助你实现目标。

# 本书适合谁

如果你是一个事件响应人员、网络安全调查员、系统管理员、恶意软件分析员、取证专家、学生，或者是对学习或提高恶意软件分析技能感兴趣的安全专业人员，那么本书适合你。

# 本书内容

[第一章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_82)，*恶意软件分析简介*，向读者介绍了恶意软件分析的概念、恶意软件分析的类型，以及如何建立一个隔离的恶意软件分析实验环境。

[第二章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_368)，*静态分析*，教授从恶意二进制文件中提取元数据的工具和技术。它展示了如何比较和分类恶意软件样本。你将学习如何在不执行程序的情况下确定二进制文件的各个方面。

[第三章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_522)，*动态分析*，教授确定恶意软件行为及其与系统交互的工具和技术。你将学习如何获取与恶意软件相关的网络和主机指示器。

[第四章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_584)，*汇编语言与反汇编基础*，提供汇编语言的基本理解，并教授进行代码分析所需的基本技能。

[第五章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_676)，*使用 IDA 反汇编*，介绍*IDA Pro*反汇编器的功能，你将学习如何使用*IDA Pro*进行静态代码分析（反汇编）。

[第六章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_885)，*调试恶意二进制文件*，教授使用*x64dbg*和*IDA Pro*调试器调试二进制文件的技术。你将学习如何使用调试器控制程序的执行并操纵程序的行为。

[第七章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_894)，*恶意软件功能与持久性*，描述了使用逆向工程分析恶意软件的各种功能。还涉及恶意程序使用的各种持久性方法。

[第八章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_985)，*代码注入与挂钩*，教授恶意程序常用的代码注入技术，如何在合法进程中执行恶意代码。还介绍了恶意软件使用的挂钩技术，通过这些技术恶意代码能够重定向控制到恶意代码，以监控、阻止或过滤 API 的输出。你将学习如何分析使用代码注入和挂钩技术的恶意程序。

[第九章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_1061)，*恶意软件混淆技术*，涵盖恶意程序用来隐藏信息的编码、加密和包装技术。它教授了不同的策略来解码/解密数据并解包恶意二进制文件。

[第十章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_1143)，*使用内存取证狩猎恶意软件*，介绍了使用内存取证检测恶意组件的技术。你将学习使用不同的 Volatility 插件来检测和识别内存中的取证痕迹。

[第十一章](https://cdp.packtpub.com/learning_malware_analysis/wp-admin/post.php?post=1200&action=edit#post_1250)，*使用内存取证检测高级恶意软件*，介绍了高级恶意软件用来躲避取证工具的隐匿技巧。你将学习如何调查和检测用户模式和内核模式的根工具组件。

# 要最大化利用本书

熟悉编程语言，如 C 和 Python，将会有所帮助（特别是理解第 5、6、7、8 和 9 章中涵盖的概念）。如果你写过一些代码，并对编程概念有基本了解，你将能够最大化地利用本书。

如果你没有编程知识，仍然可以理解第 1、2 和 3 章中涵盖的基本恶意软件分析概念。*然而*，你可能会觉得理解其余章节中的概念稍显困难。为了帮助你赶上进度，每章都提供了足够的信息和额外的资源，你可能需要额外阅读以完全理解这些概念。

# 下载彩色图片

我们还提供了一个 PDF 文件，包含本书中使用的截图/图表的彩色图像。你可以在此下载： [`www.packtpub.com/sites/default/files/downloads/LearningMalwareAnalysis_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/LearningMalwareAnalysis_ColorImages.pdf)。

# 使用的约定

本书中使用了多种文本约定。

`CodeInText`：用于代码示例、文件夹名称、文件名、注册表键和值、文件扩展名、路径名、虚拟 URL、用户输入、函数名称和 Twitter 账号。例如：“将下载的`WebStorm-10*.dmg`磁盘映像文件作为系统中的另一个磁盘挂载。”

任何命令行输入都会以粗体突出显示，示例如下：

```
$ sudo inetsim
INetSim 1.2.6 (2016-08-29) by Matthias Eckert & Thomas Hungenberg
Using log directory: /var/log/inetsim/
Using data directory: /var/lib/inetsim/
```

当我们希望引起你对特定代码或输出部分的注意时，相关的行或项会以粗体显示：

```
$ python vol.py -f tdl3.vmem --profile=WinXPSP3x86 ldrmodules -p 880
Volatility Foundation Volatility Framework 2.6
Pid Process Base InLoad InInit InMem MappedPath
--- ----------- -------- ----- ------- ----- ----------------------------
880 svchost.exe 0x10000000 False False False \WINDOWS\system32\TDSSoiqh.dll
880 svchost.exe 0x01000000 True False True \WINDOWS\system32\svchost.exe
880 svchost.exe 0x76d30000 True True True \WINDOWS\system32\wmi.dll
880 svchost.exe 0x76f60000 True True True \WINDOWS\system32\wldap32.dll
```

*斜体*：用于新术语、重要单词或词组、恶意软件名称以及键盘组合。示例：按*Ctrl + C*复制

屏幕文本：菜单或对话框中的文字会像这样出现在正文中。示例：从管理面板中选择“系统信息”。

警告或重要提示会以这种方式出现。提示和技巧会以这种方式出现。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：请通过电子邮件`feedback@packtpub.com`与我们联系，并在邮件主题中提到书名。如果你对本书的任何内容有疑问，请发送邮件至`questions@packtpub.com`。

**勘误**：虽然我们已经尽最大努力确保内容的准确性，但错误仍然会发生。如果您在本书中发现任何错误，我们将不胜感激，如果您能向我们报告。请访问 [www.packtpub.com/submit-errata](http://www.packtpub.com/submit-errata)，选择您的书籍，点击“勘误提交表单”链接，并输入相关详情。

**盗版**：如果您在互联网上发现任何我们作品的非法复制品，我们将不胜感激，如果您能提供该素材的地址或网站名称。请通过`copyright@packtpub.com`与我们联系，并附上该资料的链接。

**如果您有兴趣成为作者**：如果您在某个领域拥有专业知识，并且有兴趣写作或为书籍贡献内容，请访问 [authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。阅读并使用本书后，为什么不在您购买书籍的网站上留下您的评论呢？潜在读者可以看到并参考您的公正意见来做出购买决策，我们也能了解您对我们产品的看法，而我们的作者也能看到您对其书籍的反馈。谢谢！

若想了解更多有关 Packt 的信息，请访问 [packtpub.com](https://www.packtpub.com/)。
