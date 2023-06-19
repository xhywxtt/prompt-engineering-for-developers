---
marp: true
theme: default
paginate: true
style: |
  h1 {
    font-family: Segoe UI;
    text-align:left;
    color: rgb(17, 30, 128);
  }
  section {
    justify-content: flex-start;
  }
  ul>li,ol>li,p {
    font-size: 34px;
  }
  ul>li>ul>li {
    font-size: 30px;
  }
  table {
    font-size: 20px;
  }
---
<style scoped>
section {
  justify-content: center;
}
p {
  margin-top: 100px;
  text-align: center;
}
</style>

# <!--fit-->Prompt engineering of GPT
Haoyue Xue
2023/6/20

---
# Outline
- Background
- Principle 1: Write clear and specific instructions
- Principle 2: Give the model time to “think”
- Principle 3: Reducing Hallucinations
- Principle 4: Ask for success
- Principle 5: Use external tools
- Iterative Prompt Develelopment
- References
---
# Background
- GPT-3, GPT-3.5, and GPT-4 models from OpenAI are prompt-based.
- Very sensitive to the prompt.
- Prompt construction an important skill to develop.
- Prompt construction can be difficult.
  - prmopt acts to configure the model weights to complete the desired task
  - more of an art than a science

<!--[A1.1]
1. OpenAI 中的 GPT-3、GPT-3.5 和 GPT-4 模型基于prompt，用户通过输入文本prompt与模型交互，模型会答复一个completion，这个completion相当于模型对我们输入prompt的延续。
2. 虽然这些模型非常强大，但它们对prompt也非常敏感，prompt的变化对模型的行为影响很大。 3. 这使得prmopt构造成为开发的重要技能。
4. prompt构造可能很困难。在实践中，提示的作用是配置模型权重以完成指定的任务，但它更像艺术而不是科学，通常需要经验和直觉来制作成功的提示。
5. 所以今天主要给大家分享一下prompt engineering中常用的一些技术以及简单的原理
-->
---
<style scoped>
section {
  justify-content: center;
}
</style>
# <!--fit-->Principle 1: Write clear and specific instructions
- Guide the model towards the desired output
- Clear ≠ Short
<!--
原则一：编写清晰、具体的指令
- 通过提供尽可能清晰和具体的指示，表达希望模型做什么，这会引导模型给出预期的输出，降低得到不正确回答的可能性。
- 注意：清晰不等于简短，因为很多时候较长的提示可能为模型提供了更清晰的描述，可以带来更详细和相关的输出。
-->
---
## Tactic 1: Include details in your query to get more relevant answers

Worse|Better
-|-
How do I add numbers in Excel?|How do I add up a row of dollar amounts in Excel? I want to do this automatically for a whole sheet of rows with all the totals ending up on the right in a column called "Total".
Who’s president?|Who was the president of Mexico in 2021, and how frequently are elections held?
Write code to calculate the Fibonacci sequence.|Write a TypeScript function to efficiently calculate the Fibonacci sequence. Comment the code liberally to explain what each piece does and why it's written that way.
Summarize the meeting notes.|Summarize the meeting notes in a single paragraph. Then write a markdown list of the speakers and each of their key points. Finally, list the next steps or action items suggested by the speakers, if any.
<!--
[G 1.1]
策略1：在查询中包含详细信息以获取更相关的答案
- 为了获得高度相关的回复，最好确保提供了重要的详细信息。否则，模型将会进行猜测。
- 这里有一些对比的例子【演示：例1、例2：斐Fěi波那契数列】
- 在看例子之前简单看一下，我这里用的是Azure上申请的GPT3.5的model，提前把key配置在环境变量里了。调用模型的时候除了messages和engine以外，还有一个temperature温度参数[A2.12]，改变温度参数会改变模型的输出。 温度是一个正数，通常在0.1到1.0之间。较低的温度值（接近0.0）会使生成的文本更加确定性、保守、具体，较高的温度值（接近1.0）会增加随机性，使生成的文本更加随机、多样化，产生更多发散的答案。
-->

---
## Tactic 2: Write clear and specific instructions
- Delimiters can be anything like: ```, """, < >, <tag> </tag>
- Help avoid prompt injections
![w:600](image.png)
<!--[W2 1.1][W3 1.1][G1.3][A2.7]
策略2：使用分隔符清晰地表示输入的不同部分
- 分隔符可以是是三个 ```，引号，XML标签、章节标题，任何可以让模型清楚地知道这是一个单独的部分的东西【演示：例1：我们给出一段话并要求 GPT 进行总结；例2：将---分隔符添加到不同的信息源或步骤之间】
- 分隔符还可以避免prompt注入，prompt注入指的是：输入里面可能包含其他指令，会覆盖掉你的指令。【演示：例3.1、例3.2：用户的输入中注入了一段其他指令】
-->
---
## Tactic 3: Ask for a structured output
- Format: JSON, HTML
- Template of output
<!--[W2 1.2][W3 1.2]
策略3：要求一个结构化的输出
1. 可以是 JSON、HTML 等格式，可以使模型的输出更容易被我们解析【演示：例1.1、例1.2：要求 GPT 生成三本书的标题、作者和类别，并要求 GPT 以 JSON、HTML 的格式返回给我们】
2. 还可以给出结果模板【演示：例2：针对淘宝用户评价的信息进行分类--效果：分类后的信息简洁明了，方便传送给不同的负责人】
-->
## Tactic 4: Ask the model to check whether conditions are satisfied
- Assumptions may not satisfied, ask to check
<!--[W2 1.3]
策略4：要求模型检查是否满足条件
- 如果任务做出的假设不一定满足，我们可以告诉模型先检查这些假设，如果不满足，让模型告诉我们并停止执行。【演示：例1：假设满足，例2：假设不满足，例2.1：告诉模型检查，例2.2：不告诉模型检查】

-->
---
## Tactic 5: "Few-shot" prompting
- Give successful examples of completing tasks
- GPT API - give a series of messages between the User and Assistant
<!--[W2 1.4][G1.5][A1.2.3、2.2]
策略5：少样本prompting
- 在某些情况下，提供示例可能比提供说明更容易。在要求模型执行实际任务之前，提供给它少量训练示例，以便为模型提供额外的上下文。【演示：例1：以一致的风格回答问题】
- 如果用API的话，可以将user和assistant之间的一系列信息作为few-shot的示例。【演示：例2】
-->
## Tactic 6: Prime the output (Cue)
- A few words at the end of prompt, helping to direct the desired output
- Often a prefix that the model can buid onto
<!--[A2.6、1.2.4]
策略6：用提示来引导输出
- 在提示的末尾包含几个字词或短语，帮助将模型定向到所需的输出。
- 通常是模型可以作为生成基础的前缀。【演示：例1：总结文章的关键点，例1.1：给Cue，例1.2：不给Cue；例2：询问一些搜索关键词，例2.1：给Cue，例2.2：不给Cue】
-->
---
<style scoped>
section {
  justify-content: center;
}
</style>
# <!--fit-->Principle 2: Give the model time to “think”
- Similar as human: Short time leads to wrong answer
- Different from human: Human text generation vs. LLM text generation
<!-- [W2 2、Y1]
原则二：给模型时间去思考
- 如果你给模型一个在短时间或用少量文字无法完成的任务，它可能会猜测错误。这种情况对人来说也是一样的。如果您让某人在没有时间计算出答案的情况下完成复杂的数学问题，他们也可能会犯错误。
- 但是，LLM生成文本的过程和人类生成文本的过程非常不同。在深入到具体策略之前，先来看一下二者的区别
-->
---
# Human text generation
- Example: "California's population is 53 times that of Alaska"
  ![height:450px](image-1.png)
<!-- [Y1]
假设你正在写一篇博客，最后写了这样一句话：加州的人口是阿拉斯加的 53 倍。
在得出这句话之前，我们的内心独白是这样的：
1. 下一步，让我比较加州和阿拉斯加的人口数。
2. OK，我先需要分别知道这两个州的人口数。
3. 我现在不知道这俩数，先查一下
4. 用维基百科，查到了加利福尼亚的人口和阿拉斯加的人口。
5. 需要把这俩数相除，但我心算算不出来，需要用计算器
6. 用计算器发现答案是 53。
7. 合理性检查：53有意义吗？ok，这个数字很大，但是加利福尼亚是人口最多的州，也许这看起来还可以。
8. OK我现在有了我可能需要的所有信息
9. 写下来：California has 53X times greater
10. 重试：措辞有点问题，删掉重新写
11. 检查：嗯，这回可以了
综上可以发现，人类在写作的时候，有很多单独的内心独白，会有一个reflect过程检查我正在写的东西，并判断它是否可以。如果不行会删除重写，直到满意。
-->
---
# LLM text generation
- Example: "California's population is 53 times that of Alaska"
![h:70px](image-2.png)
    - LLMs spend ~same amount of compute on every token
      - They don't reproduce this behavior by default
    - They don't know what they don't know, they just imitate the next token
    - They don't know what they are good at or not, they just imitate the next token.
    - They don't reflect. They don't sanity check. They don't correct their mistakes along the way. They don't have inner monologue.
- More time to think --> More tokens --> More computation resource
<!--
同样的例子，从GPT的角度来看，它只是一个token的序列。
1. 因此，当 GPT 读取或生成这些token时，GPT对每个token花几乎相同的算力，你不能指望它对每个token做太多的工作。
2. 它们不知道自己不知道什么，它们只是预测下一个token；
3. 它们不知道自己擅长什么，不擅长什么，只是尽力预测下一个token。
4. 它们不检查任何东西，它们在默认情况下不纠正它们的错误，它们的头脑中没有单独的内心独白。
回到刚刚说的原则二（给模型时间去思考）。针对复杂的任务，您可以指示模型花更多时间思考问题，这意味着它在任务上花费了更多的计算资源。
-->
---
## Tactic 1: Specify the steps required to complete a task
<!--[W2 2.1]
策略一：把任务拆分成多个步骤，指定完成任务所需的步骤【演示：例1.1：给出法语摘要，例2：结合前面原则一的策略3（要求结构化输出）给出指定格式】
-->
## Tactic 2: Chain of thought prompting (COT)
- A variation on tactic 1. Instead of splitting a task into smaller steps, the model response is instructed to proceed step-by-step and present all the steps involved.
- Reduce the possibility of inaccuracy of outcomes
- Principle: Models need tokens to think
  - Prompt model to have internal monologue
  - Spread out reasoning over more tokens
<!--[A2.10][Y2]
策略二：思维链prompting
1. 这是策略一的变体。这种方法不是将一项任务分割成较小的步骤，而是指示模型如何逐步进行，并展示出所有步骤。
2. 这样做可以减少结果不准确的可能性。
【演示：例1：给出一个问题和一个学生的解答，要求模型判断解答是否正确，例1.1：bad，例1.2：good，指导模型在下结论之前找出一个自己的解法】
3. 思维链的原理依然是：模型需要更多的tokens来思考。
   1. 用Prompt来引导他们有内在独白。
   2. 如果你的任务需要推理，您不能指望Transformer对每个token进行太多推理，因此您必须真正将推理分散到越来越多的token上。
-->
---
## Tactic 3: Ask for reflection
- LLMs (especially GPT-4) can often recognize later when their samples didn't seem to have worked out well.
- They won't revisit if you don't ask them to.
<!--[Y3]
策略三：回顾、检查之前的答案
- 特别是对于更大的模型，比如 GPT-4，有时候他很清楚自己没有完成任务。它只是在采样方面恰好不走运。
- 如果你问他“你完成任务了吗？”，它会告诉你，“不，我没有完成任务。让我再尝试一次。”。
- 如果你不要求它检查，它不会自己检查，因为它只是token simulator，所以你必须在你的prompt中推动它来检查。【演示：用ChatGPT窗口演示：押韵诗】
-->
---
## Recreating our "System 2"
- System 1 thinking: fast, automatic
  - happens automatically, intuitively, and with little effort.
  - Driven by instinct and our experiences.
- System 2 thinking: slow, deliberate
  - slower and requires more effort.
  - Conscious and logical.
<!--[Y4]
以上策略都属于 重建人类System 2的范围
- System 1是一个快速的、近乎瞬时的过程：它会自动地、凭直觉地、毫不费力地发生。它是由本能和我们的经验驱动的。
- System 2是大脑中较慢的、经过深思熟虑的计划部分。需要更多的努力。它是有意识的，逻辑性强的。
- LLM只是对token进行抽样，所以更像是System 1，而我们上述各种策略，相当于按照人类System 2的思考方式来引导它。
-->
---
# Principle 3: Reducing Hallucinations
<style scoped>
section {
  justify-content: center;
}
</style>
- Model Limitations
  - Hallucinations: Makes statements that sound plausible but are not true
- Reducing Hallucinations: Provide reference text
## Tactic 1 : Instruct the model to answer using a reference text
## Tactic 2 : Instruct the model to answer with citations from a reference text
<!--
模型的局限性之一是幻觉，即他偶尔会生成一些看似真实，实则编造的知识。
这是因为模型在训练过程中接触了大量的知识，它并没有完全记住所见的信息，因此它并不很清楚自己知识的边界。这意味着它可能会尝试回答有关晦涩主题的问题，并编造听起来合理但实际上并不正确的答案。
【演示：例1：编造一个假的产品名字，模型会一本正经地告诉我们编造的知识。】
<!--[W2 3.1][G2.1][W2 3.2][G2.2]
策略一：提供一些正确的资料给他，要求模型使用参考文本回答
策略二：要求模型从参考文本中引用答案
【演示：例2：提供文档，回答问题，给出引用】
-->

---
# Principle 4: Ask for success
- LLMs don't want to succeed.
- They want to imitate training sets with a spectrum of performance qualities.
- You want to succeed, and you should ask for it.

<!--[Y5]
- LLM 有种不想成功的心理怪癖。它们只是想模仿。如果你想成功，你应该要求它成功。
- 当 Transformer 被训练时，它们的训练集中有不同质量的数据。
  - 例如，一些物理问题，可能有一个学生的解决方案完全错误，但也可能有一个非常正确的专家答案。
  - Transformer无法区分它们之间的区别——它们知道低质量解决方案和高质量解决方案，但默认情况下，它们想要模仿所有这些，因为它们只是接受过语言建模方面的训练。
-->
---
## Tactic 1: Tell GPT "Be sure we have the right answer"
<!--
策略一：告诉GPT，确保得到正确的答案
这实际上使Transformer工作得更好，因为Transformer现在不必在低质量解决方案上对冲其概率质量
-->
## Tactic 2: Tell GPT "You are xxx (some role)"
- Ex. You are a leading expert on this ptpic. Pretend you have IQ 120
- System message
  - Prime the model with context, instructions
  - Describe the assistant’s personality
  - Define what the model should and shouldn’t answer
  - Define the format of model responses.
<!--
策略二：要求模型采用某个角色[A2.1]
- 比如可以和GPT说“你是一个某领域的专家，智商120”。不要试图要求太多的IQ，因为如果你要求 400 的IQ，你可能会在数据分布之外；或者更糟糕的是，你可能会在一些科幻的数据分布中，它会开始 进行一些科幻角色扮演，或类似的事情。
- 注意这里其实可以用“系统”这个角色。
之前的例子中有过这种，比如原则一策略2中的You are an AI assistant that helps people find information.
系统消息包含在提示的开头，用于为模型提供上下文、说明或与用例相关的其他信息。 可以使用系统消息来描述助手的个性，定义模型应回答和不应回答的内容，以及定义模型响应的格式。
-->

---
# Principle 5: Use external tools
## Tactic 1: Use external tools to perform more accurate calculations
- Code executation
- External APIs
- ChatGPT plugins
![bg right contain](image-3.png)
<!--
策略一：用外部工具执行精确计算
- 由于GPT不能独立准确地执行算术或长时间的计算。这种情况，有几个不同的方法
1. 代码执行 2. 调用外部 API 3. 使用ChatGPT的插件，如图所示ChatGPT提供了很多插件，其中包括计算器插件（不过现在貌似只有ChatGPT plus会员才能用，已经过了可以申请加入waitlist的阶段了，我也看了Azure上貌似没有这个，大家有更多信息也可以帮忙补充下）
-->
---
<!--[G5.1][Y6]-->
## Tactic 2: Tell GPT what they are not good at
"You are not very good at mental arthmetic. Whenever you need to do very large number addition, multiplication, or whatever, instead, use this calculater. Here's how you use the calculater: you use this token, combination, etc."

    - They don't know what they don't know, they just imitate the next token
    - They don't know what they are good at or not, they just imitate the next token.
<!--策略二：明确告诉GPT他们不擅长什么
例如：“你的心算不太好。每当您需要进行大数加法、乘法或其他操作时，请使用此计算器。以下是您如何使用计算器。使用这个标记组合，等等。”
这里的原因前面讲LLM text generation的时候提到过：他们不知道他们不知道什么，他们不知道他们不擅长什么
-->
---
# Iterative Prompt Develelopment
![bg left contain](image-4.png)
- Prompt guidelines
  - Give clear and specific prompt
  - Analyze why result does not give desired output
  - Refine the idea and prompt
  - Repeat ...
<!--[W2 4]
【演示：用Final Notebook 2
任务：从一个椅子的产品说明书生成一份是说明书，用于帮助营销团队为在线零售网站撰写营销式描述
问题一：生成文本太长
解决：通过要求它限制生成文本长度来解决这一问题
问题二：文本关注在错误的细节上，这个网站并不是直接向消费者销售，它实际上旨在向家具零售商销售家具，他们会更关心椅子的技术细节和材料。
解决：要求它专注于与目标受众（家具零售商）相关的方面。
问题三：需要一个表格形式的描述，因为最终要展示在网站上
解决：要求它将所有内容格式化为可以在网页使用的 HTML。
】
- 一般情况下，都无法在第一次尝试时，就想到最准确的prompt，但这没关系。只要有一个好的迭代过程，能让prompt对任务实现效果越来越好就可以。
- 迭代图：当你在写提示时，你有一个关于你想做什么的想法，然后你就可以初步尝试编写一个清晰具体的prmopt，然后运行它观察结果，如果效果不够好，就应该基于上面讲过的原则，弄清楚为什么。然后完善prompt，多次循环上述过程，直到最终得到适合的prompt。
-->

---
<style scoped>
li {
  font-size: 24px;
}
li li {
  font-size: 20px;
}
</style>
# References
1. GPT best parctices - OpenAI API: https://platform.openai.com/docs/guides/gpt-best-practices/strategy-test-changes-systematically
2. Prompt engineering techniques on Azure: https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/advanced-prompt-engineering?pivots=programming-language-chat-completions#system-message
3. ChatGPT Prompt Engineering for Developers on DeepLearning: https://learn.deeplearning.ai/chatgpt-prompt-eng/lesson/1/introduction
4. "State of GPT" presentation by Andrej Karpthy on Microsoft Build 2023: https://www.youtube.com/watch?v=bZQun8Y4L2A
- Other Tools:
  - Generate PPT from Markdown using Marp: https://marpit.marp.app/markdown
  - Jupyter Notebook:
    - Link to the examples: https://github.com/xhywxtt/prompt-engineering-for-developers/tree/main/Final%20Presentation/Final%20Notebook
<!--
以上是这次分享的参考文献，主要参考了OpenAI官网和Azure上的文档，DeepLearning上的课程，以及Microsoft 2023上关于GPT现状的演讲。
另外，我用了Marp这个工具由Markdown自动生成PPT，可以专注于内容，不用被调格式困扰（这个工具也有一个缺点就是主题很少，且生成的ppt文字无法再编辑）
/dʒuːˈpaɪtə:/
-->
