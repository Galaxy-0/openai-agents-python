# 常见代理模式

本文件夹包含了不同常见代理模式的示例。

## 确定性流程

一个常见的策略是将任务分解为一系列较小的步骤。每个任务可以由一个代理执行，一个代理的输出作为下一个代理的输入。例如，如果您的任务是生成一个故事，您可以将其分解为以下步骤：

1. 生成大纲
2. 生成故事
3. 生成结局

这些步骤中的每一个都可以由一个代理执行。一个代理的输出作为下一个代理的输入。

请查看 [`deterministic.py`](./deterministic.py) 文件以获取示例。

## 交接和路由

在许多情况下，您有专门的子代理来处理特定任务。您可以使用交接将任务路由到正确的代理。

例如，您可能有一个前线代理接收请求，然后根据请求的语言将其转交给专门的代理。
请查看 [`routing.py`](./routing.py) 文件以获取示例。

## 代理作为工具

交接的心理模型是新代理"接管"。它看到之前的对话历史，并从那时起拥有对话的所有权。然而，这不是使用代理的唯一方式。您也可以将代理用作工具 - 工具代理独立运行，然后将结果返回给原始代理。

例如，您可以将上述翻译任务建模为工具调用：与其转交给特定语言的代理，不如将代理作为工具调用，然后在下一步中使用结果。这使您可以同时翻译多种语言。

请查看 [`agents_as_tools.py`](./agents_as_tools.py) 文件以获取示例。

## LLM 作为评判者

LLM 在获得反馈后通常可以提高其输出质量。一个常见的模式是使用模型生成响应，然后使用第二个模型提供反馈。您甚至可以使用较小的模型进行初始生成，使用较大的模型进行反馈，以优化成本。

例如，您可以使用 LLM 生成故事大纲，然后使用第二个 LLM 评估大纲并提供反馈。然后您可以使用反馈来改进大纲，并重复直到 LLM 对大纲满意为止。

请查看 [`llm_as_a_judge.py`](./llm_as_a_judge.py) 文件以获取示例。

## 并行化

并行运行多个代理是一个常见的模式。这对于延迟（例如，如果您有多个相互不依赖的步骤）和其他原因都很有用，例如生成多个响应并选择最佳响应。

请查看 [`parallelization.py`](./parallelization.py) 文件以获取示例。它并行运行翻译代理多次，然后选择最佳翻译。

## 防护栏

与并行化相关，您经常需要运行输入防护栏以确保代理的输入有效。例如，如果您有一个客户支持代理，您可能想要确保用户不是在尝试请求数学问题的帮助。

您当然可以通过使用并行化来实现这一点，而不需要任何特殊的代理 SDK 功能，但我们支持特殊的防护栏原语。防护栏可以有"触发线" - 如果触发线被触发，代理执行将立即停止，并引发 `GuardrailTripwireTriggered` 异常。

这对于延迟非常有用：例如，您可能有一个运行防护栏的非常快的模型和一个运行实际代理的慢速模型。您不想等待慢速模型完成，所以防护栏让您可以快速拒绝无效输入。

请查看 [`input_guardrails.py`](./input_guardrails.py) 和 [`output_guardrails.py`](./output_guardrails.py) 文件以获取示例。

---

# Common agentic patterns

This folder contains examples of different common patterns for agents.

## Deterministic flows

A common tactic is to break down a task into a series of smaller steps. Each task can be performed by an agent, and the output of one agent is used as input to the next. For example, if your task was to generate a story, you could break it down into the following steps:

1. Generate an outline
2. Generate the story
3. Generate the ending

Each of these steps can be performed by an agent. The output of one agent is used as input to the next.

See the [`deterministic.py`](./deterministic.py) file for an example of this.

## Handoffs and routing

In many situations, you have specialized sub-agents that handle specific tasks. You can use handoffs to route the task to the right agent.

For example, you might have a frontline agent that receives a request, and then hands off to a specialized agent based on the language of the request.
See the [`routing.py`](./routing.py) file for an example of this.

## Agents as tools

The mental model for handoffs is that the new agent "takes over". It sees the previous conversation history, and owns the conversation from that point onwards. However, this is not the only way to use agents. You can also use agents as a tool - the tool agent goes off and runs on its own, and then returns the result to the original agent.

For example, you could model the translation task above as tool calls instead: rather than handing over to the language-specific agent, you could call the agent as a tool, and then use the result in the next step. This enables things like translating multiple languages at once.

See the [`agents_as_tools.py`](./agents_as_tools.py) file for an example of this.

## LLM-as-a-judge

LLMs can often improve the quality of their output if given feedback. A common pattern is to generate a response using a model, and then use a second model to provide feedback. You can even use a small model for the initial generation and a larger model for the feedback, to optimize cost.

For example, you could use an LLM to generate an outline for a story, and then use a second LLM to evaluate the outline and provide feedback. You can then use the feedback to improve the outline, and repeat until the LLM is satisfied with the outline.

See the [`llm_as_a_judge.py`](./llm_as_a_judge.py) file for an example of this.

## Parallelization

Running multiple agents in parallel is a common pattern. This can be useful for both latency (e.g. if you have multiple steps that don't depend on each other) and also for other reasons e.g. generating multiple responses and picking the best one.

See the [`parallelization.py`](./parallelization.py) file for an example of this. It runs a translation agent multiple times in parallel, and then picks the best translation.

## Guardrails

Related to parallelization, you often want to run input guardrails to make sure the inputs to your agents are valid. For example, if you have a customer support agent, you might want to make sure that the user isn't trying to ask for help with a math problem.

You can definitely do this without any special Agents SDK features by using parallelization, but we support a special guardrail primitive. Guardrails can have a "tripwire" - if the tripwire is triggered, the agent execution will immediately stop and a `GuardrailTripwireTriggered` exception will be raised.

This is really useful for latency: for example, you might have a very fast model that runs the guardrail and a slow model that runs the actual agent. You wouldn't want to wait for the slow model to finish, so guardrails let you quickly reject invalid inputs.

See the [`input_guardrails.py`](./input_guardrails.py) and [`output_guardrails.py`](./output_guardrails.py) files for examples.
