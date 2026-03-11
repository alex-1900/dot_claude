# DSL 函数定义

本文档定义了 prompt-parser 支持的 DSL 函数。设计为可扩展结构，新增函数只需在此添加定义。

## 函数列表

### `#dep(ClassName).method(method1, method2, ...)`

标识任务相关的类和方法。

**解析模板：**
```
本次任务与 {ClassName} 类的 {methods} {count} 个方法有关
```

**示例：**
- 输入：`#dep(OrderController).method(createOrder, pay)`
- 输出：`本次任务与 OrderController 类的 createOrder, pay 2 个方法有关`

---

### `#dep(ClassName).field(field1, field2, ...)`

标识任务相关的类和字段。

**解析模板：**
```
本次任务涉及 {ClassName} 类的 {fields} {count} 个字段
```

**示例：**
- 输入：`#dep(User).field(name, email, phone)`
- 输出：`本次任务涉及 User 类的 name, email, phone 3 个字段`

---

### `#api(endpoint).action(action1, action2, ...)`

标识任务相关的 API 端点和操作。

**解析模板：**
```
本次任务涉及 API 端点 {endpoint} 的 {actions} {count} 个操作
```

**示例：**
- 输入：`#api(/users).action(GET, POST, DELETE)`
- 输出：`本次任务涉及 API 端点 /users 的 GET, POST, DELETE 3 个操作`

---

### `#table(tableName).column(col1, col2, ...)`

标识任务相关的数据表和列。

**解析模板：**
```
本次任务涉及数据表 {tableName} 的 {columns} {count} 个列
```

**示例：**
- 输入：`#table(orders).column(id, user_id, amount, status)`
- 输出：`本次任务涉及数据表 orders 的 id, user_id, amount, status 4 个列`

---

## 添加新函数

要添加新的 DSL 函数，请按以下格式定义：

```markdown
### `#functionName(param1, param2).subFunction(item1, item2, ...)`

[功能描述]

**解析模板：**
```
[输出模板，使用 {paramName} 作为占位符]
```

**示例：**
- 输入：`[示例输入]`
- 输出：`[示例输出]`
```

---

## 通用解析规则

1. 函数名以 `#` 开头
2. 主参数在 `()` 中，子函数参数在 `.subFunction()` 中
3. 多个参数用 `,` 分隔
4. 参数两端的空格会被自动去除
5. 解析时自动计算参数数量并输出