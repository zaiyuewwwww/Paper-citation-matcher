# Paper Citation Matcher

`paper-citation-matcher` 是一个适用于 Codex 的学术写作 skill，用于对论文正文进行**逐句或 1-2 句级别的文献匹配、补引、去重和终检**，尤其适合处理 `.docx` 文稿。

它特别适合下面这类任务：

- 先建立 citation matching table，再决定是否回写到正文
- 按 section 补充引用，而不是整篇一次性处理
- 优先保留用户已经高亮或手工标记的文献
- 优先寻找新的文献，避免全文过度依赖少数几篇来源
- 控制 section 内和全文内的重复引用
- 检查 citation coverage，确保每句或至少每两句有一个引用
- 在补完引用后同步更新参考文献和匹配表

---

## 这个 skill 可以做什么

这个 skill 主要有三个模式：

### 1. 建立文献匹配表

适合在**不直接修改正文**的情况下，先对某一部分建立逐句文献匹配表。

它会：

- 读取指定 section
- 拆成句子级或 1-2 句级 block
- 识别已用文献和手工标记文献
- 优先寻找新的候选文献
- 导出结构化匹配表

### 2. 将 citation 写回正文

在匹配表确认后，把 citation 回写进指定 section。

它会：

- 尽量保留原句
- 只做最小化 wording 调整
- 补充缺失的参考文献
- 不改动无关 section

### 3. 最终引用审计

在回写完成后，对正文做最后一轮检查和修正。

它会：

- 检查引用覆盖率
- 检查重复引用
- 检查句子与文献是否匹配
- 检查分区要求
- 检查正文引用与文后参考文献是否对应
- 同步更新 citation table

---

## 核心工作流程

推荐按下面顺序使用：

1. 读取目标 section
2. 建立 citation matching table
3. 审核匹配结果
4. 将 citation 写回正文
5. 先做 `coverage pass`
6. 再做 `repetition pass`
7. 最后做 `citation-table update pass`
8. 执行最终审计

这个顺序是固定的，原因是：

- `coverage pass` 优先解决“有没有引用”的问题
- `repetition pass` 再解决“重复太多”的问题
- `table update pass` 保证最终表格和正文一致

---

## 这个 skill 的几个默认习惯

下面这些规则反映的是原作者的写作习惯，不是所有用户都必须照搬。你完全可以根据自己的工作方式修改。

### 1. 使用 `【】` 或 `[]` 手工标记文献

这个 skill 会把 `【】` 或 `[]` 中的内容视为“用户已经选择或已经引用过的文献”的强信号。

例如：

- `【Insomnia, emotions, and job satisfaction】`
- `【Scott, B.A., Judge, T.A., 2006. Insomnia, emotions, and job satisfaction: A multilevel study.】`
- `【Scott and Judge, 2006】`
- `[Good gig, bad gig]`

默认识别优先级是：

1. 文献标题
2. 完整文献引用
3. 作者-年份

也就是说，`【标题】` 的优先级最高。

#### 为什么这样设计

- 很多论文草稿阶段，作者会先把候选文献标题写在 `【】` 或 `[]` 里
- 这些内容不应该被系统当作“新文献”重新推荐一遍
- 它们应该进入“已用文献池”，参与去重和复用判断

#### 如何自定义

- 如果你完全不用 `【】` 或 `[]`，可以删除这个规则
- 如果你希望 `【作者-年份】` 和 `【标题】` 权重相同，可以调整优先级
- 如果你只把 `【】` 当成“候选文献”而不是“已引用文献”，也可以改规则

---

### 2. 新文献优先

当多个候选文献都能支撑某句话时，默认优先选择：

- 全文中还没出现过的文献
- 或使用频次较低的文献

#### 为什么这样设计

- 避免全文反复依赖少数几篇文献
- 提高文献覆盖面
- 让 section 内的引用分布更自然

#### 如何自定义

- 如果你更看重经典文献，可以弱化“新文献优先”
- 如果你更严格控制复用，可以设定更低的 repetition threshold

---

### 3. coverage 先于 repetition

补引后的终检顺序默认是：

1. `coverage pass`
2. `repetition pass`
3. `citation-table update pass`

#### 为什么这样设计

- 缺 citation 比 citation 重复更优先需要修正
- 应先确保每句或每两句有 citation
- 然后再清理重复引用

#### 如何自定义

- 如果你更在意“绝不重复”，可以把两步合并
- 如果你对 coverage 要求没那么严格，也可以放宽标准

---

### 4. 默认 citation density 标准

默认标准是：

- 理想情况：每一句都有 citation
- 最低要求：每两句至少共享一个清晰相关的 citation

短的过渡句可以和前后句共享 citation，但前提是它们属于同一个论点链条。

#### 如何自定义

- 如果你要更严格：改成“每句必须有 citation”
- 如果你要更宽松：改成“每段至少一个 citation”

---

## JCR / Quartile 筛选

这个公开版**不绑定任何个人电脑上的本地 JCR 文件路径**。

它的设计是：

- 如果用户提供 JCR / quartile 表，就使用
- 如果用户没有提供，就继续文献匹配流程，但把 quartile 标记为 `not checked`
- 如果匹配不到分区，就标记为 `unverified`

推荐的 JCR 表结构：

- 一个期刊名称列
- 一个 quartile 列
- quartile 值例如 `Q1`、`Q2`、`Q3`、`Q4`

---

## 输出的文献匹配表包含什么

默认情况下，citation matching table 会包含这些字段：

- `Block`
- `Original sentence block`
- `Recommended literature`
- `Link(s)`
- `Journal / JCR quartile`
- `Basis for citation`
- `Evidence type`
- `Highlighted source retained?`
- `Manually marked in 【】 / []?`
- `Manual marker resolved?`
- `Already cited in manuscript?`
- `Citation count in manuscript`
- `New citation preferred?`
- `Reuse rationale`
- `Citation coverage status`
- `Needs additional citation?`
- `Coverage note`
- `Added in coverage pass?`
- `Replaced in repetition pass?`
- `Repeated citation retained?`

这些字段的作用是：

- 不仅记录“用了哪篇”
- 还记录“为什么用它”
- 以及“它是不是 coverage 阶段新增的”“是不是 repetition 阶段替换的”

---

## 推荐的使用方式

最稳妥的使用流程是：

1. 先只建表，不改正文
2. 确认文献匹配表
3. 再把 citation 写回正文
4. 最后跑终检

这样做的好处是：

- 过程透明
- 方便人工审核
- 不容易把不合适的 citation 直接写进文稿

---

## 示例 prompt

### 只建立匹配表

```text
请使用 paper-citation-matcher，阅读我的 Discussion 部分，识别【】和[]中的手工文献标记，先建立逐句 citation matching table。优先使用新的 Q1-Q2 文献，并尽量避免重复引用。
```

### 把 citation 写回正文

```text
请使用 paper-citation-matcher，根据刚确认的匹配表，把 citation 写回 Theory and Hypotheses 部分，并补齐文后参考文献。
```

### 进行最终审计

```text
请使用 paper-citation-matcher，对刚修改的部分做最终审计：先补 coverage，再检查重复引用，最后同步更新 citation table。
```

---

## 目录结构

```text
paper-citation-matcher-public/
├── SKILL.md
├── README.md
├── README_zh.md
├── agents/
│   └── openai.yaml
└── references/
    ├── workflow.md
    ├── jcr_rule.md
    └── output_schema.md
```

---

## 如果你要按自己的习惯修改

最常需要改的地方有：

- [SKILL.md](/Applications/Stata/ado/personal/UKHLSBHPS/flexible_work/slpqua-jbsat/paper-citation-matcher-public/SKILL.md)
  主规则、优先级、覆盖率和去重逻辑
- [references/workflow.md](/Applications/Stata/ado/personal/UKHLSBHPS/flexible_work/slpqua-jbsat/paper-citation-matcher-public/references/workflow.md)
  工作流程和终检顺序
- [references/jcr_rule.md](/Applications/Stata/ado/personal/UKHLSBHPS/flexible_work/slpqua-jbsat/paper-citation-matcher-public/references/jcr_rule.md)
  quartile 筛选方式
- [references/output_schema.md](/Applications/Stata/ado/personal/UKHLSBHPS/flexible_work/slpqua-jbsat/paper-citation-matcher-public/references/output_schema.md)
  匹配表字段
- [agents/openai.yaml](/Applications/Stata/ado/personal/UKHLSBHPS/flexible_work/slpqua-jbsat/paper-citation-matcher-public/agents/openai.yaml)
  GitHub / Codex 中的显示名称与默认说明

如果你的写作习惯和这个 skill 的默认习惯不一样，建议直接把规则改明，而不要依赖隐含约定。
