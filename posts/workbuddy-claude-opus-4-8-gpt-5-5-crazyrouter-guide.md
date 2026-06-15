---
title: 'WorkBuddy 接入 claude-opus-4-8 与 gpt-5.5：用 Crazyrouter 一键配置自定义模型'
published: true
description: '这篇中文指南从 models.json、PowerShell 一键配置、模型选择、Token 权限、成本控制、稳定性和排错等维度，讲解如何在 WorkBuddy 中接入 claude-opus-4-8、gpt-5.5 等 Crazyrouter 自定义模型。'
tags: ai, api, tutorial, productivity
cover_image: 'https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/workbuddy-claude-opus-4-8-gpt-5-5-crazyrouter-guide-cover.png'
canonical_url: 'https://crazyrouter.com/blog/workbuddy-claude-opus-4-8-gpt-5-5-crazyrouter-guide?utm_source=devto&utm_medium=article&utm_campaign=workbuddy_claude_opus_48_gpt55_2026'
---

# WorkBuddy 接入 claude-opus-4-8 与 gpt-5.5：用 Crazyrouter 一键配置自定义模型的完整指南

WorkBuddy 支持自定义模型配置，这让它不再局限于默认模型列表。只要服务端提供 OpenAI-compatible API，就可以把新的模型能力接入 WorkBuddy，比如 `claude-opus-4-8`、`gpt-5.5`、`claude-sonnet-4-6`、`gpt-5.4` 等。

对于开发者来说，真正的问题通常不是“能不能接入”，而是：

- WorkBuddy 的 `models.json` 应该怎么改？
- `claude-opus-4-8` 和 `gpt-5.5` 适合放在什么使用场景？
- 自定义模型 URL 要不要带 `/v1`？
- API Key、模型 ID、能力字段应该怎么填？
- 多个模型如何统一管理，避免配置混乱？
- 团队使用时如何控制 Token 权限和调用成本？

这篇文章基于 WorkBuddy 自定义模型配置方式，结合 Crazyrouter 的 OpenAI-compatible API，讲清楚如何用一条 PowerShell 命令把最新模型写入 WorkBuddy，并从模型选择、配置结构、稳定性、成本、团队管理、安全和排错等多个维度做一次完整拆解。

![WorkBuddy 接入 claude-opus-4-8 与 gpt-5.5 的配置流程](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/workbuddy-claude-opus-4-8-gpt-5-5-crazyrouter-guide-01.png)

---

## 一、为什么要在 WorkBuddy 里配置 claude-opus-4-8 和 gpt-5.5？

WorkBuddy 这类 AI 工作流工具，核心价值不只是“能聊天”，而是把模型能力嵌入到日常开发、文档处理、代码理解、自动化任务和团队流程里。

当模型能力变化很快时，默认模型列表往往跟不上实际需求。通过自定义模型，你可以把新模型快速接入 WorkBuddy，而不必等待客户端更新。

例如：

```text
claude-opus-4-8
gpt-5.5
claude-sonnet-4-6
gpt-5.4
```

这些模型可以分别用于不同任务：

| 模型 | 更适合的场景 |
|---|---|
| `claude-opus-4-8` | 长文档分析、复杂推理、代码架构审查、多步骤任务规划 |
| `gpt-5.5` | 通用开发助手、代码生成、问题排查、产品文档、工具调用场景 |
| `claude-sonnet-4-6` | 日常编码、批量文本处理、稳定响应、成本和效果平衡 |
| `gpt-5.4` | 常规问答、轻量代码修改、配置说明、自动化脚本辅助 |

如果你在 WorkBuddy 里只配置一个模型，很容易出现“大模型处理小任务浪费，小模型处理复杂任务吃力”的问题。更合理的方式是：把多个模型放进 WorkBuddy，然后按任务选择。

---

## 二、WorkBuddy 自定义模型的本质：写入 models.json

WorkBuddy 的自定义模型配置，本质上是写入本地配置文件：

```text
%USERPROFILE%\.workbuddy\models.json
```

PowerShell 里对应路径通常是：

```powershell
$HOME\.workbuddy\models.json
```

一个简化后的自定义模型配置大概是这样：

```json
{
  "id": "gpt-5.5",
  "name": "gpt-5.5",
  "vendor": "Custom",
  "url": "https://cn.crazyrouter.com/v1",
  "apiKey": "sk-your-api-key",
  "supportsToolCall": true,
  "supportsImages": true,
  "supportsReasoning": true,
  "useCustomProtocol": false
}
```

关键字段可以这样理解：

| 字段 | 作用 |
|---|---|
| `id` | 模型真实 ID，WorkBuddy 调用时会使用它 |
| `name` | 在 WorkBuddy 界面里展示的名称 |
| `vendor` | 自定义模型通常写 `Custom` |
| `url` | API Base URL，OpenAI-compatible 服务一般需要 `/v1` |
| `apiKey` | 调用接口使用的 Key |
| `supportsToolCall` | 是否支持工具调用 |
| `supportsImages` | 是否支持图片输入 |
| `supportsReasoning` | 是否支持推理能力 |
| `useCustomProtocol` | 是否使用 WorkBuddy 的自定义协议，OpenAI-compatible 通常为 `false` |

手动编辑也能完成配置，但容易出现 JSON 格式错误、URL 少 `/v1`、模型重复、覆盖旧配置等问题。所以更推荐用脚本自动写入。

---

## 三、一条 PowerShell 命令接入 Crazyrouter 自定义模型

如果你想快速把 Crazyrouter 的模型写入 WorkBuddy，可以使用这个开源脚本：

```text
https://github.com/xujfcn/workbuddy-crazyrouter
```

在 Windows PowerShell 中执行：

```powershell
iwr https://raw.githubusercontent.com/xujfcn/workbuddy-crazyrouter/main/setup-workbuddy-crazyrouter.ps1 -UseB | iex
```

脚本会提示输入 Crazyrouter API Key，然后自动完成：

- 创建或读取 `%USERPROFILE%\.workbuddy\models.json`
- 写入 Crazyrouter 自定义模型
- 自动补齐 `/v1` API 路径
- 写入 `claude-opus-4-8`、`gpt-5.5` 等模型
- 对同名模型去重
- 保留原有其它模型配置
- 修改前自动备份旧文件

如果不想交互输入 Key，也可以先设置环境变量：

```powershell
$env:CRAZYROUTER_API_KEY="sk-你的CrazyrouterKey"
iwr https://raw.githubusercontent.com/xujfcn/workbuddy-crazyrouter/main/setup-workbuddy-crazyrouter.ps1 -UseB | iex
```

执行完成后，完全退出 WorkBuddy，再重新打开，然后在模型列表里选择 `Custom` 或自定义模型。

---

## 四、模型维度：claude-opus-4-8 与 gpt-5.5 怎么选？

把模型接进去只是第一步，更重要的是知道什么时候该用哪个模型。

### 1. 复杂推理与长上下文：优先 claude-opus-4-8

如果你的任务包含大量上下文，例如：

- 阅读长篇技术文档
- 分析大型代码仓库
- 梳理产品需求
- 生成复杂迁移方案
- 审查架构设计
- 多轮上下文推理

可以优先尝试 `claude-opus-4-8`。

它更适合“慢一点但要想清楚”的任务。比如让 WorkBuddy 读取多个文件后，给出模块边界、潜在风险和重构路径，这类任务对上下文理解和推理稳定性要求更高。

### 2. 通用开发与生产力：优先 gpt-5.5

如果你的任务是日常开发助手，例如：

- 写代码片段
- 改 Bug
- 解释报错
- 生成测试用例
- 写 README
- 设计 API 调用示例
- 处理常见 DevOps 配置

可以优先使用 `gpt-5.5`。

它更适合高频、通用、响应速度和综合能力都重要的场景。对于 WorkBuddy 里的大多数日常任务，`gpt-5.5` 可以作为默认主力模型。

### 3. 成本和稳定性平衡：保留中间档模型

除了两个最新模型，也建议保留一些中间档模型，例如：

```text
claude-sonnet-4-6
gpt-5.4
```

它们适合处理：

- 中等复杂度代码任务
- 批量文本整理
- 普通摘要
- 配置文件解释
- 简单工作流自动化

这样可以避免所有任务都走最高规格模型。

---

## 五、配置维度：为什么 Base URL 必须注意 `/v1`？

很多 WorkBuddy 自定义模型配置失败，不是 API Key 错了，也不是模型不能用，而是 URL 写错了。

OpenAI-compatible API 通常需要这样的 Base URL：

```text
https://cn.crazyrouter.com/v1
```

而不是：

```text
https://cn.crazyrouter.com
```

少了 `/v1` 后，客户端拼接接口路径时可能会请求到错误地址。

PowerShell 脚本里会做 URL 规范化：

```powershell
$normalized = $Url.Trim().TrimEnd("/")
if ($normalized -notmatch "/v1$") {
    $normalized = "$normalized/v1"
}
```

也就是说，你即使输入：

```text
https://cn.crazyrouter.com
```

脚本也会自动变成：

```text
https://cn.crazyrouter.com/v1
```

这个小细节可以减少很多“看起来都填对了，但 WorkBuddy 就是请求失败”的问题。

---

## 六、团队管理维度：Token 权限不要随便全开

个人使用时，一个 API Key 配多个模型通常就够了。但团队使用 WorkBuddy 时，建议对 Token 做更细粒度的管理。

可以按场景创建不同 Token：

| Token 类型 | 建议权限 |
|---|---|
| 开发 Token | 允许 `gpt-5.5`、`claude-sonnet-4-6` 等日常模型 |
| 高阶分析 Token | 允许 `claude-opus-4-8` 等高能力模型 |
| 测试 Token | 设置较低额度，用于测试脚本和集成 |
| 自动化 Token | 只允许工作流需要的模型，避免误调用 |

这样做有几个好处：

1. 避免所有人都无意中调用高规格模型。
2. 可以按项目或成员定位调用来源。
3. 某个 Token 出问题时可以单独停用，不影响整个账户。
4. 可以用额度限制控制异常脚本或死循环调用。

但要注意：如果 Token 开了模型限制，WorkBuddy 里配置的模型必须在 Token 允许列表中。否则即使账户余额充足，也可能因为 Token 不允许该模型而报错。

---

## 七、成本维度：不要所有任务都默认最高模型

WorkBuddy 接入多个模型后，建议建立一个简单的使用策略。

可以按任务复杂度选择：

| 任务类型 | 推荐模型 |
|---|---|
| 简单问答、短文本改写 | `gpt-5.4` 或中间档模型 |
| 日常代码生成、Bug 排查 | `gpt-5.5` |
| 长文档理解、复杂架构分析 | `claude-opus-4-8` |
| 批量摘要、格式转换 | 中间档模型 |
| 多文件代码审查 | `claude-opus-4-8` 或 `gpt-5.5` |

一个实用原则是：

> 先用中间档模型处理常规任务，只在复杂推理、长上下文和高价值任务上使用最高规格模型。

这不是单纯为了省调用成本，也是为了让团队形成更稳定的模型使用习惯。

---

## 八、稳定性维度：配置前后都要可恢复

修改 WorkBuddy 的 `models.json` 前，一定要有备份。

脚本默认会生成类似这样的备份文件：

```text
%USERPROFILE%\.workbuddy\models.json.bak.20260615-120000
```

如果配置后 WorkBuddy 模型列表异常，可以这样恢复：

1. 完全退出 WorkBuddy。
2. 打开 `%USERPROFILE%\.workbuddy\`。
3. 删除当前 `models.json`。
4. 把备份文件重命名为 `models.json`。
5. 重新打开 WorkBuddy。

如果你想清理旧 Crazyrouter 模型，只保留本次写入的模型，可以下载脚本后执行：

```powershell
.\setup-workbuddy-crazyrouter.ps1 -ReplaceCrazyrouter
```

这适合模型列表已经重复、旧 URL 和新 URL 混在一起、想重新整理配置的情况。

---

## 九、安全维度：一键命令方便，但最好先看源码

PowerShell 一键命令很方便：

```powershell
iwr https://raw.githubusercontent.com/xujfcn/workbuddy-crazyrouter/main/setup-workbuddy-crazyrouter.ps1 -UseB | iex
```

但从安全角度，`| iex` 的含义是“下载后立即执行”。如果你在公司电脑、生产环境或安全要求较高的机器上操作，更推荐先下载并阅读：

```powershell
iwr https://raw.githubusercontent.com/xujfcn/workbuddy-crazyrouter/main/setup-workbuddy-crazyrouter.ps1 -OutFile setup-workbuddy-crazyrouter.ps1
```

确认脚本内容后再执行：

```powershell
.\setup-workbuddy-crazyrouter.ps1
```

API Key 默认只会写入本机 WorkBuddy 配置文件，不需要上传到脚本仓库或其它地方。

---

## 十、排错维度：WorkBuddy 没看到模型怎么办？

如果执行脚本后 WorkBuddy 没有出现 `claude-opus-4-8` 或 `gpt-5.5`，可以按下面顺序排查。

### 1. 是否完全重启 WorkBuddy？

关闭窗口不一定等于退出进程。建议从托盘或任务管理器确认 WorkBuddy 已完全退出，再重新打开。

### 2. models.json 是否写入成功？

检查文件：

```text
%USERPROFILE%\.workbuddy\models.json
```

确认里面是否包含：

```text
claude-opus-4-8
gpt-5.5
```

### 3. URL 是否为 /v1 结尾？

应为：

```text
https://cn.crazyrouter.com/v1
```

不是：

```text
https://cn.crazyrouter.com
```

### 4. Token 是否允许访问该模型？

如果 Token 设置了模型白名单，需要确认 `claude-opus-4-8`、`gpt-5.5` 等模型已经被加入允许列表。

如果不确定，可以临时取消 Token 模型限制测试。

### 5. Token 是否设置了额度限制？

有些错误不是账户余额不足，而是 Token 自身设置了额度限制，并且该 Token 额度已经耗尽。

这时需要进入令牌管理页面，检查该 Token 的额度限制，而不是只看账户余额。

---

![WorkBuddy 模型选择与 Token 权限管理矩阵](https://raw.githubusercontent.com/xujfcn/images/main/blog/posts/workbuddy-claude-opus-4-8-gpt-5-5-crazyrouter-guide-02.png)

## 十一、推荐配置方案

如果你只是个人开发者，可以先用默认配置：

```text
claude-opus-4-8
claude-opus-4-7
claude-sonnet-4-6
gpt-5.5
gpt-5.4
```

如果是团队使用，可以按角色拆分：

### 开发者日常 Token

```text
gpt-5.5
claude-sonnet-4-6
gpt-5.4
```

### 架构与长文档 Token

```text
claude-opus-4-8
gpt-5.5
```

### 自动化脚本 Token

```text
gpt-5.4
claude-sonnet-4-6
```

这样既能让 WorkBuddy 用上最新模型，也能避免模型权限和成本控制混在一起。

---

## 十二、完整操作流程总结

如果你想用 Crazyrouter 把 `claude-opus-4-8` 和 `gpt-5.5` 接入 WorkBuddy，可以按下面步骤操作：

### 第一步：准备 Crazyrouter API Key

在 Crazyrouter 控制台创建或复制一个可用 Key。

### 第二步：打开 PowerShell

建议使用普通用户权限即可，不需要管理员权限。

### 第三步：执行一键配置命令

```powershell
iwr https://raw.githubusercontent.com/xujfcn/workbuddy-crazyrouter/main/setup-workbuddy-crazyrouter.ps1 -UseB | iex
```

或者使用环境变量：

```powershell
$env:CRAZYROUTER_API_KEY="sk-你的CrazyrouterKey"
iwr https://raw.githubusercontent.com/xujfcn/workbuddy-crazyrouter/main/setup-workbuddy-crazyrouter.ps1 -UseB | iex
```

### 第四步：重启 WorkBuddy

完全退出并重新打开 WorkBuddy。

### 第五步：选择 Custom 模型

在 WorkBuddy 模型列表中选择自定义模型，例如：

```text
claude-opus-4-8
gpt-5.5
```

### 第六步：按任务选择模型

- 日常开发：`gpt-5.5`
- 长上下文分析：`claude-opus-4-8`
- 中等任务：`claude-sonnet-4-6` 或 `gpt-5.4`

---

## 总结

WorkBuddy 自定义模型配置的关键，不在于手动写几行 JSON，而在于把模型、Token、URL、权限、成本和团队使用方式统一管理起来。

通过 Crazyrouter 的 OpenAI-compatible API，你可以把 `claude-opus-4-8`、`gpt-5.5` 等最新模型快速接入 WorkBuddy；通过 PowerShell 脚本，可以自动完成 `models.json` 写入、备份、去重和 `/v1` 规范化。

对个人开发者来说，这意味着更快用上新模型；对团队来说，这意味着可以用 Token 权限、模型白名单和额度限制，把 WorkBuddy 的模型使用变得更可控。

项目地址：

```text
https://github.com/xujfcn/workbuddy-crazyrouter
```

一键配置命令：

```powershell
iwr https://raw.githubusercontent.com/xujfcn/workbuddy-crazyrouter/main/setup-workbuddy-crazyrouter.ps1 -UseB | iex
```
