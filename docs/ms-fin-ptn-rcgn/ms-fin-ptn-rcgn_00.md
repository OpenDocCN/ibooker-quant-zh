# 前言

> 发现模式是智慧的本质。
> 
> 丹尼斯·普雷格

随着技术进步和金融信息的去中心化，编码和自动化研究已经成为交易世界中不可或缺的部分。掌握交易和编码艺术的人在市场上拥有巨大的优势。

交易技术是众多的，它们可以基于许多工具和概念。例如，基本分析交易员依赖经济和政治分析来获取不同资产的长期视角，而技术交易员则依赖更多的量化指标和少数心理概念来预测市场下一个可能的走势。

因此，我们可以说在高层次上存在两种类型的分析，基本分析和技术分析。本书将详细介绍技术分析中的一个领域，称为*蜡烛图案识别*。

## 为什么这本书？

我整个职业生涯都在研究交易策略、模式以及与金融世界有关的任何事物。我对模式，特别是蜡烛图案，有了特别的热情，因为它们被广泛采用，但也因其有趣的表现结果。此外，多年来，我发现了一些蜡烛图案，我相信至少可以与经典模式媲美。这也是我写这本书的目的：我打算呈现所有蜡烛图案的完整性，包括我个人的图案，以及如何编写一个系统来跨多种市场对它们进行回测。

由于其客观性，机器可以比人类更好地执行模式识别和检测。因此，我已经把书的前几章专门用来创建蜡烛图案识别算法的结构，然后再深入研究后续章节中的模式和策略。这意味着您将学会的第一个技能是如何在 Python 中自动化数据导入过程。

有许多经典的蜡烛图形式，每个人都有责任测试它们以确定它们是否真的具有预测性。毕竟，如果我们用这些模式来预测市场，我们应该有客观的结果来证明它们确实是增值的。我们将获得这样的结果并解释它们，就像我多年来发现的蜡烛图案一样。我们还将看到每种模式的优势和局限性。

当我们找到能够帮助预测任务的好模式时，您应该将它们插入到整体交易框架中，其中包括其他工具和风险管理系统。您将学习如何编写技术指标并将它们与蜡烛图案结合以生成交易信号。最后，您将对这些信号进行回测，并能够优化参数，从而获得一个良好的全面模式识别策略。

因此，本书的实用性在于向您展示如何通过让您创建的算法评估不同的蜡烛图案来自动化您的研究。最后，您将学习如何确定您的策略，该策略将使用这些模式并与其他技术指标结合。

## 目标受众

本书适合有志于学习的学生、学者、好奇的头脑以及对蜡烛图案识别及其在金融中应用感兴趣的金融从业者。如果你不仅对使用 Python 感兴趣，还对开发策略和技术指标感兴趣，那么你将从本书中受益。

本书假设您在 Python 编程和金融交易方面具有基础知识（专业 Python 用户将发现代码非常直观）。我采用了一种清晰简单的方法，重点放在关键概念上，以便您理解每个想法的目的。

# 本书使用的约定

以下是本书使用的排版约定：

*Italic*

指示新术语、网址、电子邮件地址、文件名和文件扩展名。

`Constant width`

用于程序列表，以及段落内引用程序元素（如变量或函数名、数据库、数据类型、环境变量、语句和关键字）。

**`Constant width bold`**

显示用户应按照字面意义键入的命令或其他文本。

*`Constant width italic`*

显示应由用户提供的值或由上下文确定的值替换的文本。

###### 提示

此元素表示提示或建议。

###### 注意

此元素表示一般注释。

###### 警告

此元素表示警告或注意事项。

# 使用代码示例

可以从[*https://github.com/sofienkaabar/mastering-financial-pattern-recognition*](https://github.com/sofienkaabar/mastering-financial-pattern-recognition)下载补充材料（示例代码、练习等）。

如果您有技术问题或使用代码示例遇到问题，请发送电子邮件至*bookquestions@oreilly.com*。

本书的目的是帮助你完成工作。一般情况下，如果本书提供示例代码，你可以在你的程序和文档中使用它。除非你复制了大部分代码，否则无需联系我们获取许可。例如，编写一个使用本书多个代码片段的程序不需要许可。销售或分发 O'Reilly 书籍中的示例代码需要许可。引用本书并引用示例代码来回答问题无需许可。将本书的大量示例代码整合到产品文档中需要许可。

我们感谢但通常不要求归因。归因通常包括标题、作者、出版商和 ISBN。例如：“*掌握金融模式识别*，索菲恩·卡巴尔（奥莱利）。版权所有 2023 年索菲恩·卡巴尔，978-1-098-12047-4。”

如果您认为您对代码示例的使用超出了合理使用或以上许可的范围，请随时通过*permissions@oreilly.com*与我们联系。

# 奥莱利在线学习

###### 注意事项

40 多年来，[*奥莱利传媒*](https://oreilly.com)提供技术和商业培训、知识和洞察力，帮助公司取得成功。

我们独特的专家和创新者网络通过图书、文章和我们的在线学习平台分享他们的知识和专业知识。奥莱利的在线学习平台为您提供按需访问直播培训课程、深入学习路径、交互式编码环境以及来自奥莱利和 200 多家其他出版商的广泛文本和视频。有关更多信息，请访问[*https://oreilly.com*](https://oreilly.com)。

# 如何联系我们

请将有关本书的评论和问题发送至出版商：

+   奥莱利传媒公司

+   北格拉文斯坦公路 1005 号

+   CA 95472，塞巴斯托波尔

+   800-998-9938（美国或加拿大）

+   707-829-0515（国际或本地）

+   707-829-0104（传真）

我们为本书设有网页，列出勘误、示例和任何其他信息。您可以通过[*https://oreil.ly/mstrg-finan-pttrn-recog*](https://oreil.ly/mstrg-finan-pttrn-recog)访问此页面。

发送电子邮件至*bookquestions@oreilly.com*发表评论或提出有关本书的技术问题。

欲获取有关我们的图书和课程的新闻和信息，请访问[*https://oreilly.com*](https://oreilly.com)。

在 LinkedIn 上找到我们：[*https://linkedin.com/company/oreilly-media*](https://linkedin.com/company/oreilly-media)。

在 Twitter 上关注我们：[*https://twitter.com/oreillymedia*](https://twitter.com/oreillymedia)。

在 YouTube 上关注我们：[*https://youtube.com/oreillymedia*](https://youtube.com/oreillymedia)。

# 致谢

没有我父母的支持，一切都将不同，这也是我必须承认他们对本书直接和间接影响的原因。我还要提到，没有我妻子夏琳的耐心，我无法完成深夜写作。我对她的感激之情溢于言表。

我还要感谢编辑米歇尔·史密斯和科宾·柯林斯以及制作编辑伊丽莎白·费姆的持续支持和出色工作，也感谢他们的耐心。同样，我要感谢所有参与奥莱利传媒的每个人。

另外，我要特别感谢出色的技术审阅人员，宁王、蒂莫西·基普和库珊·沃拉，他们对这本书的巨大贡献不可估量。他们对使这本书易读、有用和简单的影响深远。我无法找到比他们更合适的人来审阅我的书。

最后，我深深感谢您，读者，您抽出宝贵的时间阅读我的作品并信任我的研究。希望您觉得它有用。
