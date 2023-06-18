# Youtube视频 Y
□ 参考其他文章：Few-shot Prompting： https://learningprompt.wiki/docs/chatGPT/tutorial-extras/Few-Shot%20Prompting
1) Human text generation vs. LLM text generation
  1) Human text generation：rich internal monologue（长篇独白）and tool use
  2) LLM text generation（GPT）：

    a) just a sequence of tokens, when GPT is reading or generating these tokens, it just goes chunk, chunk, chunk，每个token几乎是同样的计算量
    b) Transformer有大约80层推理（reasoning），do its best to imitate（模仿）
    c) 整个过程和人类非常不同
    d) Transformer就像是token simulator，他们只是imitate（模仿）下一个token
2) Chain of thought

  1) 如果你的任务需要reasoning（推理），你不能期待Transformer对于每个token做太多的推理（reasoning）
  2) 你需要扩展reasoning到越来越多的tokens
  3) 不能给一个特别复杂的问题，然后期待答案只是一个single token，因为没有足够的时间
  4) These transformers need tokens to think
  5) 方式一：Few-shot-CoT：transformer会模仿这个template（例b）
  6) 方式二：直接说“let's think step-by-step”（例d）：训练transformer展示它的工作过程，更容易成功，因为making slower reasoning over time
3) Self-consistency & Ask for reflection
  1) Self-consistency：是LLM的特性

    a) 在推理时，会走入死胡同，但和人不同，它无法恢复
    b) 它们会继续这个序列，即使它们知道这个序列不会成功。
    c) 解决方法：让它们有某种能力能够回顾、检查或尝试
  2) Ask for reflection：具体解决技术
    a) 其实LLM知道他们犯错了（screw up）
    b) 例如，假设您要求模型生成一首不押韵的诗：【例--ppt】

      } 但如果你不再通过prompt，问它，他就不会revisit，所以你必须在你的prompt中推动它来检查。如果你不要求它检查，它不会自己检查，因为它只是token simulator
4) Recreating our System 2 <-- 以上技巧都可概括为这个
  1) 人类的System 1 vs System 2
    a) System 1: 快速的自动过程，有点对应于 LLM，只是对标记进行抽样。
    b) System 2:大脑中较慢的、经过深思熟虑的计划部分。
  2) Tree of Thought
    a) 5/17发的论文 https://arxiv.org/abs/2305.10601
    b) 作者提议对于任何给定的prompt，维护多个completions，在整个过程中对它们进行评分，并保留那些进展顺利的。
    c) 实际上是与一些 Python 胶水代码一起使用的prompt，因为你实际上要维护多个prompt，你还必须在这里做一些树搜索算法来找出扩展哪个prompt等等。
      i) 因此，它是 Python 胶水代码和在 while 循环或更大算法中调用的各个提示的共生体。
    d) 和AlphaGo有相似之处：
      i) AlphaGo下围棋有一个放下一块棋子的策略，这个策略本来就是模仿人训练出来的。
      ii) 但是除了这个策略之外，它还会做蒙特卡洛搜索，它会在围棋中打出多种可能性并评估所有这些，只保留那些运作良好的。
  3) Chains / Agents

    a) 人们开始真正探索更通用的技术，不仅仅是简单的问答提示，而是看起来更像是将许多提示串在一起的 Python 胶水代码。
    b) 在右边，我有一个来自这篇论文的例子，叫做ReAct: Synergizing Reasoning and Acting in Language Models（ https://ai.googleblog.com/2022/11/react-synergizing-reasoning-and-acting.html )，他们将提示的答案构造为一系列思考、行动、观察、思考、行动、观察，这是一个完整的展开，一种思考 回答查询的过程。在这些动作中，模型也被允许使用工具。

      Comparison of four prompting methods, (a) Standard, (b) Chain of thought (CoT, Reason Only), (c) Act-only, and (d) ReAct, solving a HotpotQA question. In-context examples are omitted, and only the task trajectory is shown. ReAct is able to retrieve information to support reasoning, while also using reasoning to target what to retrieve next, demonstrating a synergy of reasoning and acting.
    c) 在左边，我有一个AutoGPT 的例子。
      i) 顺便说一句，AutoGPT 是一个我认为最近炒得沸沸扬扬的项目，但我仍然觉得它有点鼓舞人心。它是一个允许 LLM 保留任务列表并继续递归分解任务的项目，我认为目前效果不是很好，不建议人们在实际应用中使用它。
      ii) 但我认为，随着时间的推移，这是可以从中汲取灵感的东西。AutoGPT有点像让我们的模型系统思考。
5) LLM 有种不想成功的心理怪癖。它们只是想模仿。如果你想成功，你应该要求它。
  1) 当 Transformer 被训练时，它们的训练集中有不同质量的数据。
    a) 例如，一些物理问题，可能有一个学生的解决方案完全错误，但也可能有一个非常正确的专家答案。
    b) Transformer无法区分它们之间的区别——它们知道低质量解决方案和高质量解决方案，但默认情况下，它们想要模仿所有这些，因为它们只是接受过语言建模方面的训练。
  2) 在测试的时候，你必须ask for a good performance。
  3) 各种提示对比
    ◊ “let's think step by step”非常强大，因为它把推理分散到许多标记上
    ◊ 但更好的提示方法是："let's work this out in a step-by-step way to be sure we have the right answer"。
      } 这实际上使Transformer工作得更好，因为Transformer现在不必在低质量解决方案上对冲其概率质量
  4) 所以，应该Ask for a strong solution
    a) 例如：You are a leading expert on this topic. Pretend you have IQ 120, etc.
      i) 不要试图要求太多的IQ，因为如果你要求 400 的IQ，你可能会在数据分布之外；或者更糟糕的是，你可能会在一些科幻的数据分布中，它会开始 进行一些科幻角色扮演，或类似的事情。
6) Tool use / Plugins
  ® 尝试ChatGPT插件：一篇文章告诉你：ChatGPT新增「插件商店」怎么打开？怎么下载？怎么使用？（真·保姆级教程） - 知乎 (zhihu.com)——发现现在只有plus会员才能用
  ®
    ◊ 为LLM提供计算器、代码解释器等，以及进行搜索的能力，并且有很多技术可以做到这一点。
    ◊ Transformers may not know what they don't know 默认情况下这些Transformer可能不知道它们不知道的事情. The model doesn't know what it's good at or not good at
    ◊ 你甚至可能想在提示中告诉Transformer，“你的心算不太好。每当您需要进行大数加法、乘法或其他操作时，请使用此计算器。以下是您如何使用计算器。使用这个标记组合，等等，等等。”


7) Retrieval-Augmented LLMs 检索增强模型
  1) 我们从一个只有检索的世界走到钟摆摆动的另一个极端，那里只有LLM的memory
  2) 但实际上，在这两者之间有 检索增强模型 的整个空间，这在实践中非常有效
  3) The context window of a transformer is its working memory，如果您可以将与任务相关的任何信息加载到工作内存中，那么该模型将运行得非常好，因为它可以立即访问所有内存。
  4) 在上图底部，我有一个Llama索引的例子，它是许多不同类型数据的一个数据连接器，你可以索引所有这些数据，让 LLM 访问它。

  5) 新兴的秘诀是获取相关文档，将它们分成chunk，将它们全部embed，得到表示该数据的embedding vectors，将其存储在vector store中；然后在测试时，对vector store进行查询，获取可能与您的任务相关的chunk，然后将它们填充到prompt中，然后生成。这在实践中可以很好地工作。
  6) 类比人解决问题的时候，你可以凭记忆做任何事情，Transformer的记忆力非常大，但更好的是，可以给一些基本的document让他参考
    无论何时，您发现自己要回到教科书上找东西，或者每当您发现自己要回到图书馆的文档中查找东西时，Transformer肯定也想这样做。您对库的某些文档如何工作有一定的记忆，但最好查找一下。同样的事情也适用于LLM。
8) Constraint prompting：forcing a certain template in the outputs of LLMs
  1) 例：强制 LLM 的输出将是 JSON
    a) 这实际上将保证输出将采用这种形式，因为它们进入并扰乱了来自Transformer的所有不同标记的概率，并且固定住这些标记。
9) Finetuning
  1) Change the weights（权重） of the model
  2) 现在在实践中做到这一点变得越来越容易，这是因为最近开发了许多技术并拥有库调用。

    a) 例：LoRA：参数高效微调技术可确保您只训练模型的小而稀疏的部分。因此大部分模型都保持在基础模型上，并且允许更改其中的一些部分， 这在经验上仍然很有效，并且使得仅调整模型的一小部分成本更低。
    b) 开源的高质量基础模型。目前，正如我提到的，我认为 Llama 相当不错，尽管我认为它现在还没有获得商业许可。
    c) SFT 是可以实现的，因为你只是在继续语言建模任务。它相对简单；
    d) 但 RLHF 是一个非常多的研究领域，而且它更难开始工作。
      我可能不建议有人尝试推出他们自己的 RLHF 实现。这些东西非常不稳定，很难训练，现在对初学者来说不是很友好，而且它也有可能变化得非常快。
10) Default recommendations

  我会把你的任务分成两个主要部分。
  1) 第一，实现你的最佳表现
    a) 首先，目前最好的性能来自 GPT4 模型。它是迄今为止功能最强大的模型。
    b) 然后， 让提示里包含详细的任务内容、相关信息和说明。想想如果它们不能给你回邮件你会告诉它们什么。要记住任务承包商是人，他们有内心独白，他们非常聪明；而LLM不具备这些品质。因此，请务必仔细考虑LLM的心理，并迎合这一点。甚至向这些提示添加任何相关的上下文和信息。
    c) 获取并且添加相关内容或信息到prompt中。多参考很多prompt engineering techniques。我在上面的幻灯片中突出显示了其中一些，但这是一个非常大的空间，我只建议您在线寻找快速的Prompt工程技术。那里有很多内容。
    d) 尝试使用少样本few-shots示例提示。这指的是你不只是想问，你还想尽可能地展示（你想要的），给它举例子，如果可以的话，帮助它真正理解你的意思。
    e) 尝试使用工具和插件来分担 LLM 本身难以完成的任务。
    f) 然后不仅要考虑单个提示和答案，还要考虑潜在的链条和反射，以及如何将它们粘合在一起，以及如何制作多个样本等。
    g) 最后，如果你认为你已经最大化了提示工程的效果，我认为你应该坚持一段时间，看看一些可能对你的应用程序的模型微调，但预计这会更慢并且涉及更多。
    h) 然后这里有一个脆弱的专家研究区，我想说的是 RLHF，如果你能让它工作的话。它目前确实比 SFT 好一点，但是，我想说的是，这非常复杂。
  2) 第二，按照这个顺序优化你的费用。
    为了优化您的成本，请尝试探索容量较低的模型（比如GPT3.5）或更短的prompt等。
11) Use Cases

  1) Limitations of LLMs
    a) 模型可能有偏见
    b) 它们可能捏造、产生幻觉信息
    c) 它们可能有推理错误
    d) 它们可能在整个类别的应用程序中都挣扎，例如：拼写相关的tasks
    e) 它们有知识截止日期，比如说，2021 年 9 月之后的事情
    f) Twitter 每天都在发生大量对LLM的攻击，包括即时注入、越狱攻击、数据中毒攻击等
  2) 我现在的建议是
    a) 在低风险应用程序中使用 LLM，将它们与始终与人工监督结合起来
    b) 将它们用作灵感和建议的来源
    c) 并考虑副驾驶而不是在某处执行任务的完全自主的代理。目前尚不清楚这些模型是否合适。
