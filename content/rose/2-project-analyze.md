# 项目剖析

![](./images/separator-5-centimeter.jpeg)

这道题其实上展示了许多项目上业已发生以及正在发生着的情景：项目逻辑纠结复杂、没有或只有很少帮不上忙的测试、阅读和维护困难。偶有热血欲挥刀重构者，会发现缺乏有效的测试做保护网；想要添加测试，又因为逻辑复杂难以切入；逻辑复杂，又只有以重构来改善。如此往复，便消磨了许多开发者的心志。

如何重构这么一份大而复杂的代码，确实是个令人头疼的问题。

有的同学拿到这个题目，下意识直接去读代码，尝试理解它的逻辑。**但这是个坑**。代码所以祖传，正因为它逻辑特别复杂、特别纠结、特别难理解。想先读懂它再动手，恐怕只能自求多福了。

另一些抱头冥思苦想的同学会说：“我在看需求描述，然后从需求描述出发，先写一些单元测试。”然而，祖传代码之乱，恐怕根本没有“单元”可言，找到切入点补充单元测试并不容易；其次，你怎能确定需求描述与代码的实现逻辑是一样的呢？

直接上手一顿狂改——同样不是好的方式。没有测试，你怎么能确信软件的现有行为没有被破坏呢？但是测试又很难添补，难道没有别的办法了吗？

**不需理解代码含义就能对付祖传代码的办法，是有的**。学习重构的基本手法和技巧是必要的，同时，养成重构的精神和正气同样重要。

要有什么样的精神呢？要有**日拱一卒、长期作战的精神**。在踏上重构的征程前，要摈弃毕其功于一役的急躁想法，每天做一点点修改，日积月累地就能形成可观的改善。上节课学到的“随时停止”的技能，就能帮你每次安全地前进一点点。

针对遗留代码，第一步永远是补上测试的安全网——不一定是单元测试，而是针对系统可观测输出的测试，它的粒度有时候可能较粗。这张初步的测试网是开展后续工作的基点。

有了测试，仍要徐图打算，不宜一下对最复杂的代码发起挑战。此阶段的基本方法是**化整为零**：先对周边的代码做些简单的清理。可以简单如变量重命名，修复些 IDE 的错误提示等。不要小瞧这些小动作带来的心理暗示，它能逐步建立你的节奏和信心。

再然后，一般可以通过提炼函数、建立命名的方式来逐步理顺代码的结构。这个过程中，你会发现随着你对代码结构的了解加深，坏味道会逐渐浮现。有了坏味道，怎么解决就是按图索骥的问题了——这张图，就是《重构》一书的第 3 章：代码的坏味道。

在重构遇到困难的时候，你可能需要时不时翻阅手上这张坏味道的地图，需要不时查阅地图上为你指出的道路——也就是一个个的重构手法。无论如何，如果你感觉步子太大了，就撤销刚刚的修改，回到安全的状态。你随时可以重整旗鼓，振作再来。

一旦熟悉重构的基本顺序和节奏，你将会发现这种节奏令人上瘾，并且你能逐步累积小的修改成为更大的修改，最终达到整洁代码的彼岸。现在，让我们小试牛刀，先来开始第一个训练吧！
