# Azure  A

1. Introduction
  1) 前言【ppt】
  2) Prompt components
    i) instructions：instructions to the model on what to do.
		ii) Primary Content：text that is being processed or transformed by the model
		iii) Examples：“one-shot” or “few-shot” learning.
		iv) Cue（暗示，提示）：jumpstart （快速启动）for the output of the model, helping to direct the model to the desired output. often a prefix that the model can build onto.
		v) Supporting content：information that the model can utilize to influence the output in some way.  和Primary Content的区别是，它不是main target of task，但它典型会和Primary Content一起使用。通常的例子包含情境信息例如当前日期，用户名字，用户偏好等。

2. Techniques
  1) System message 系统消息：包含在提示的开头，用于为模型提供上下文、说明或与用例相关的其他信息。 可以使用系统消息来描述助手的个性，定义模型应回答和不应回答的内容，以及定义模型响应的格式。
  2) Few-shot learning 少样本学习：在提示中提供一组训练示例，以便为模型提供额外的上下文。
  6) Prime the output 引导输出：在提示的末尾包含几个字词或短语，以获取遵循所需形式的模型响应 (Cue)
  7) Add clear syntax 添加明确的语法
  8) Break the task down 分解任务
  10) Chain of thought prompting 思维链提示
  11) Specifying the output structure 指定输出结构
  12) 温度
  13) Provide grounding context 提供基础上下文