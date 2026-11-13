# deep-test

一个面向 AI 编程代理的 Agent Skill：先让代理完整跑一轮普通测试，再用用户指定的时间比例跑一轮只找漏网问题的深度测试。

普通测试报告作为排除清单，深度测试轮只补普通测试经常漏掉的方向：Web 接口以外的安全、数字与格式变体、去重与归一化、时间边界、持久化、数据新鲜度。

## 工作方式

1. 被触发后只问一个问题：深度测试花普通测试实际用时 T 的多少（60% / 100% / 200% / 自填）。
2. 派一个通用子代理按固定指令跑普通测试并记录实际用时 T，普通测试报告保持原样。
3. 普通测试结束后才读取 `references/deep-procedure.md`，按预算 B = 所选比例 × T 执行深度测试。
4. 在工程根目录产出 `DEEP_TEST_REPORT_<yyyy-mm-dd>.md`，并给出摘要。

## 触发条件

仅当用户原话要求深度测试时触发，例如「深度测试」「深测」「深度测试这个工程」「deep test」「deep-test」。

普通的测试、QA、找 bug 请求不触发。

## 目录结构

```text
deep-test/
├── SKILL.md
└── references/
    ├── deep-procedure.md
    └── EFFECTIVENESS.md
```

## 安装

复制整个目录到 Agent Skill 注册目录，保持 `SKILL.md` 与 `references/` 的相对位置不变：

- CodeArts Agent 及兼容 Agent Skills 的客户端：`~/.codeartsdoer/skills/deep-test`
- 通用位置：`~/.agents/skills/deep-test`

安装注意：

- 必须使用真实目录副本，安装后修改源目录不会自动同步。
- Windows 下目录联接（junction）不会被部分扫描器识别；符号链接可用。
- `SKILL.md` 必须是文件名，frontmatter 只保留 `name` 与 `description`。

## 使用

在目标工程中让代理执行「深度测试这个工程」，或在英文环境中使用「deep test this project」，然后按提示选择时间比例。

深度测试的权限边界、检查清单与报告模板见 `references/deep-procedure.md`。

## 有效性

`references/EFFECTIVENESS.md` 记录了两个真实项目上的评测数据：深度测试补上了多次普通测试都没找到的根因，问题集中在普通测试检出率最低的方向。

仓库维护者的一次端到端验证结果：普通测试轮 543 秒发现 14 条问题，深度测试轮 452 秒新增 5 条已复现问题，6 项安全检查全部覆盖，项目源码哈希与修改时间未变。

## 许可

本项目使用 Apache License 2.0，完整条款见 [LICENSE](LICENSE)。

Copyright 2026 choumi (uglycatathome)

## 贡献

Issue 与 Pull Request 都欢迎。提交前请保持 `SKILL.md` 的 frontmatter 结构，并确认 `references/` 中的相对路径可解析。