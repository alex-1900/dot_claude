---
name: prompt-parser
description: 解析包含词汇对照表和自定义 DSL 的用户 prompt，将其润色成通顺易理解的完整任务描述，并在用户确认后执行。当用户明确引用此 skill 时触发。
---

# Prompt Parser

将用户的简略输入解析并润色成清晰完整的任务描述。

## 工作流程

### 1. 解析阶段

识别并解析用户输入中的以下元素：

**词汇对照表**
- 预定义对照表位于 `references/glossary.json`，启动时加载
- 用户可在输入中使用 `$key: value` 格式临时定义或覆盖
- 示例：`$my: orders, users` → `MySQL 表: orders, users`

**DSL 函数调用**
- DSL 函数定义位于 `references/dsl.md`
- 目前支持的函数：
  - `#dep(ClassName).method(...)` - 类和方法
  - `#dep(ClassName).field(...)` - 类和字段
  - `#api(endpoint).action(...)` - API 端点和操作
  - `#table(tableName).column(...)` - 数据表和列
- 解析为自然语言描述

### 2. 润色阶段

根据解析结果，结合项目上下文润色 prompt：
- 如果涉及特定类/方法，探索项目了解其功能
- 将用户的简略描述扩展为完整的任务说明
- 保持原意，提升可读性

### 3. 确认阶段

**必须**在执行任务前展示润色后的 prompt，格式如下：

```markdown
## 解析结果

[解析后的各部分内容]

## 润色后的任务描述

[完整的任务描述]

---
请确认是否按此任务执行？如有修改意见请告知。
```

等待用户确认后再开始执行实际任务。

## 示例

**输入：**
```
创建订单业务日志
$my: orders, users, products
#dep(OrderController).method(createOrder, pay, payment_webhook)
添加用户下单全流程的日志记录
```

**输出：**
```markdown
## 解析结果

创建订单业务日志
MySQL 表: orders, users, products
本次任务与 OrderController 类的 `createOrder`, `pay`, `payment_webhook` 3 个方法有关

## 润色后的任务描述

为订单业务添加日志记录功能。涉及的 MySQL 表包括 orders、users、products。需要关注 OrderController 类中的三个方法：createOrder（创建订单）、pay（支付）、payment_webhook（支付回调），记录用户从创建订单到支付完成的完整行为日志。

---
请确认是否按此任务执行？如有修改意见请告知。
```

## 参考文件

- `references/glossary.json` - 预定义词汇对照表
- `references/dsl.md` - DSL 函数定义（可扩展）

## 注意事项

- 词汇对照表可能出现在输入的任意位置，需要识别并应用
- DSL 函数可以有多个，需要全部解析
- 润色时应结合项目实际情况，但不要过度推断
- 如果用户输入中没有对照表或 DSL，则直接润色原文
- 用户新增 DSL 函数只需更新 `references/dsl.md`