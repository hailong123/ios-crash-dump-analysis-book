# Xcode 内置工具

Xcode为开发人员在理解和预防崩溃方面提供了重要的帮助。

Xcode 给我们提供了两个层面的帮助，对于简单的现象，Xcode 会直接告诉我们常见的错误并建议我们更正，而对于复杂情况，Xcode 会为我们提供原始的信息，但是这需要我们利用操作系统的相关知识来解释这些信息。

后续文章中我们将会多次提及 Xcode 的配置、设置和工具。但是尽管如此，让我们首先看看Xcode 提供的简单但有效的帮助。

## Xcode 诊断设置

通过打开项目 `icdab_sample`，并查看 Scheme （选择 Edit Scheme），然后选中 Diagnostics 选项，我们看到以下内容：

![](screenshots/diagnostic_settings.png)

### 执行方法

如果我们的崩溃可以从我们自己的开发环境或源码中得到复现，那么接下来的操作是我们应该打开适当的诊断设置项，然后重新运行我们的应用程序。

随着我们对各种诊断项的熟悉，我们会知道要选择哪个选项。我们将研究不同的场景，以便我们了解如何使用每个诊断设置项。但是，当我们开始了解诊断设置项的价值时，我们需要一步一步的了解一下可选项。基本方法是：

1.  对遇到的问题编写单元测试用例或 UI 测试用例。
1.  我们猜测分析并仅选择上述诊断项中的一个。
1.  执行我们的测试用例
1.  观察Xcode中的任意警告或打印信息。
1.  如果并没有解释问题，重复上述操作，但是选择另外的诊断项。 

### 分析方法

另一种用于分析和避免崩溃的方法是运行代码分析器。使用`Command-Shift-B` 调用。

下面是 `icdab_sample` 的分析报告:

```
/Users/faisalm/dev/icdab/source/icdab_sample/icdab_sample/
macAddress.m:22:12:
 warning: Null pointer argument in call to string length function
    assert(strlen(nullPointer) == 0);
```

很方便的给我们标记了源码。

![](screenshots/analyser_null.png)

我们可以在项目构建时判断是否启用该功能，对于 `shallow` 或 `deep` 模式的选择，可以根据我们的认知权衡是应该使用较慢但是更为彻底的分析还是较快但是简单的分析。它在 Xcode 项目文件的 Build Settings 选项中。

![](screenshots/static_analyser_build.png)

对于从未生成代码分析报告的大型项目来说，其输出的数据可能是海量的。报告中会有一些问题，但是总体来说做的很好。报告中会有重复，因为某些类型的错误会在整个代码中重复出现。

如果我们使用敏捷开发的方法来开发迭代软件，则可以将代码分析列为待办事项，可以在分配给重构和维护的时间内进行处理。

在大型软件项目中，重构和维护应该占整体工作的 20% 左右。这一部分出现了很多不同观点。笔者认为可以与正常开发同时进行，只要正在进行的工作没有出现高风险的变化即可。对于有高风险的变化，可以留到应用程序完成重大更新之后进行代码扥西。通常，在应用发布之后会有一段时间进行下个版本的规划，这段时间允许开发者去解决此类问题。

#### iOS QuickEdit App 案例研究

从经济学的角度来看，利用代码分析发现潜在崩溃，不失为一个解决问题的很好的投资。例如，在 Quickedit iOS 应用程序中，大约有100万行 Objective-C 代码，每天有 7 万活跃用户，当运行代分析后发现 13 个明显的崩溃问题。 我们创建了一个开发任务（“修复明显的代码分析错误”）。所有的 13 个问题在一天之内得到了修复然后测试在花费了两天进行测试。 对用户来说，崩溃现象是难以接受的。在生产环境发现的问题通常比开发环境要多付出 20 倍的努力和成本。由于用户群里的数量，可能让崩溃的影响更为严重，这 13 问题可能需要我们浪费 `20 * 3 = 60` 天的工作量。

由于年代的原因，QuickEdit 应用使用手动引用计数的 Objective-C 代码。但是尽管如此，基于应用的代码分析，它的可靠性为  99.5%。一旦这些问题得到解决，大概只需要花费 5% 的工作量就可以保持工程的稳定性。

### 方法论

一个有效的从我们应用程序中减少崩溃的方法，尤其是当我们在大型组织中时，是将代码分析纳入我们的软件开发过程中。

当开发人员拉取服务器代码时，请确保开发者不会引入新的分析警告。我们可以把代码分析报告当做被免费提供的自动进行的 `code review`。尤其对独立开发者来说，没有其他人进行 `code review`，特别有用。

将代码提交到功能分支时，请在其上运行自动化测试，并设置不同的诊断项配置。这可以自动解决问题。

每次发布之前，分配一些时间使用 `memory profiler` 去运行一些特定的测试用例，来查看内存使用情况或其他关键指标。记录内存峰值以及配置文件。然后，当以后发布版本时，我们就有了一个标准，从数量和质量两个维度来分析新版本。

## 行百里者半九十

大多数软件开发人员都知道他们 "应该" 做什么；整洁的代码、适当的测试、代码评审等。

我们建议采取针对性的方法。花费时间去一起研究示例程序来理解概念。去写一个原型代码只需要证明一个业务用例即可。去编写更多人使用并高度信赖的代码。

我们认为应该尽可能的去思考经济成本，因为大多数开发者都参与了专业的软件开发。或者，当我们从事非商业项目或业余项目时，那么经济成本就是我们的业余时间，我们更加希望能够有效的利用我们的时间。

我们建议：

- 对于示例程序和概念的研究，直接去编写代码。
- 对于原型开发来说，当我们遇到问题时去执行方法就行。
- 对于独立开发的产品，从一开始就进行代码分析，并在发现重大问题时添加到我们的开发流程中。在开始开发时可以带着编写用例，但是可以在运到重大问题时才去执行。
- 对于团队开发的产品，请添加到开发流程中。从开始开发时就进行全面的测试。