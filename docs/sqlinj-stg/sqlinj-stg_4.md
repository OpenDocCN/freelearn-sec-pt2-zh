# 第五章：评估

# 第一章

1.  数据库是一种以永久的方式存储数据的方式，通常以结构化和可访问的方式存储，以便其内容可以轻松地进行查询。

1.  关系型数据库是一种使用表格来存储数据的数据库。这些表格建模对象及其之间的关系，正如名字所示。

1.  **结构化查询语言**(**SQL**) 是一种用于与关系型数据库交互的语言，它依赖于通过简单语法构建查询。

1.  一些基于 SQL 的系统示例包括 MySQL、SQLite、Microsoft SQL Server 和 Oracle 数据库。

1.  `SELECT` 是最常见的 SQL 语句，它负责查询数据库并根据指定的要求返回数据。

1.  SQL 注入是一种通过输入数据插入任意 SQL 命令的攻击。这可能导致对目标应用程序正在使用的数据库进行本应受限的操作。

# 第二章

1.  SQL 注入可以通过使用特定字符触发，这些字符在 SQL 语法中对应特定的功能，例如字符串定界符，用于在不打算终止输入字符串的地方终止输入字符串。这样可以在后面插入 SQL 代码，以及注释字符，使系统忽略查询的某些部分。

1.  攻击者可以通过一个网页表单插入任意查询，返回可能相关的信息。他们可以查询默认表来查看数据库结构，方法是将结果附加到现有查询结果中，使用`UNION`，并通过注释掉输入后要使用的查询部分来绕过验证。

1.  攻击者可以通过两种方式绕过用户认证：他们可以从先前的注入攻击中获取密码，或者通过插入一个始终为真的语句来欺骗应用程序，从而导致登录成功。

1.  盲 SQL 注入是一种不依赖数据库输出的 SQL 注入技术。它可以依赖布尔表达式，前提是应用程序在结果为真或假时表现不同，或者如果应用程序在输出上没有任何差异，它可以引入时间延迟。

1.  由于在结果为真或假的情况下，输出没有显著差异，我们能做的就是依赖基于时间的 SQL 注入。

1.  几乎任何依赖查询的数据库系统，如果输入未经过消毒或验证，都可能容易受到注入攻击。

# 第三章

1.  虚拟化软件是一种完全模拟系统的特殊软件。我们使用它来确保我们的测试不涉及外部第三方，这意味着我们的一切都在一个受控环境中进行。

1.  Kali Linux 是一个特殊的 Linux 发行版，包含一套为安全专业人员准备的软件。我们需要它来展示针对 Web 应用程序的自动化 SQL 注入攻击。

1.  OWASP BWA 项目是一个以虚拟机形式存在的集合，包含故意设计有漏洞的 web 应用程序。我们通常使用它作为我们 web 应用攻击的目标。

1.  我们模拟 web 服务，它代表了与传统 web 应用程序不同的接口，以及移动设备，在移动环境中显示漏洞。

1.  绝对不行。只能对属于你的系统进行测试。未经明确和正式表达同意（即合同）的情况下，绝不可对属于第三方的系统进行测试。

# 第四章

1.  二分查找在进行盲 SQL 注入时尤为有用，可以逐个字符猜测，例如使用 MySQL 的`ascii()`函数。

1.  将 SQL 查询插入依赖外部语言/模块的函数参数中（如`ExtractValue()`函数）可能会在 SQL 错误中返回查询结果。

1.  OWASP ZAP 提供了 Spider 工具，用于识别网页和可能存在漏洞的表单，另外还有 Scan 和 Fuzzer 工具，可以用于检查易受攻击的参数。

1.  是的。sqlmap 具有一个密码破解模块，可以通过暴力破解从哈希值中提取密码。

1.  SQL 注入可以针对任何类型的应用程序执行，包括 web 应用程序、web 服务和移动应用程序，如本章所示。

# 第五章

1.  用户输入应始终视为潜在的恶意输入，因此必须始终进行清理和验证。

1.  验证输入意味着在接受输入之前决定它是否有效。黑名单会阻止所有已知的无效输入，而白名单仅接受已知的有效输入。

1.  参数化查询是一种构建查询语句的方法，它将查询字符串分解为参数。这些参数首先作为变量存储，然后插入到查询的主体中。

1.  字符编码和转义对于转化可能会被 SQL 解析的有害字符非常有用，这些字符可能会导致 SQL 注入攻击。

1.  **Web 应用防火墙**（**WAF**）的目的是在应用层过滤请求，并识别和防止危险的请求到达应用程序。

1.  不。如果攻击者获得了加密数据，他们也可能获得加密密钥，从而使加密变得毫无意义。

# 第六章

1.  安全漏洞通常是代码错误、配置错误或协议问题的结果。

1.  安全专业人员在测试漏洞时，必须评估漏洞的可利用性，从而正确评估其影响和风险。

1.  首先，发现漏洞。发现之后进行测试，以评估其风险。最后，基于已识别的风险，制定修复计划。

1.  SQL 注入，尽管是一个老旧的漏洞，依然在实际环境中存在，并且在今天仍然是一个问题。

1.  SQL 注入作为一种注入问题，位居 OWASP Web 应用安全风险十大漏洞之首。
