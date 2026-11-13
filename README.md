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

本 skill 遵循通用的 Agent Skill 目录约定：一个 skill 就是一个以 `SKILL.md` 为入口的目录，`references/`、`scripts/` 等同级资源一律用相对路径引用。不同宿主的注册机制不同，但只要满足下面的契约，就能被识别和加载。

### 通用安装契约

1. 目录名与 frontmatter 中的 `name` 一致（`deep-test`）。
2. `SKILL.md` 位于目录根部，文件名保持大写。
3. `references/` 与 `SKILL.md` 同级，内部文件的相对路径不变。
4. 使用真实目录；需要与源目录保持同步时用符号链接，不要用 Windows 目录联接（junction），部分扫描器不会跟随。

### 常见接入方式

按宿主提供的机制任选一种：

- **用户级目录**：把整个目录放入宿主的用户级 skill 根目录，通常形如 `~/.<host>/skills/deep-test`，对该用户的所有项目生效。
- **项目级目录**：把整个目录放入工程内的约定位置（例如 `.agents/skills/deep-test` 或宿主文档给出的目录），随项目一起提交，协作者克隆后即可使用。
- **配置或环境变量**：若宿主允许声明额外的 skill 搜索路径，把本目录加入该路径，无需复制。
- **安装器或插件市场**：若宿主提供 skill 安装器，可用"从本地目录导入"、"从压缩包导入"或"从 Git 仓库安装"；本仓库根目录本身就是合法的 skill 目录。
- **手动注册**：少数宿主需要显式登记，在配置文件中写入 `SKILL.md` 的路径即可。

### 安装后验证

1. 重新加载宿主或新开一个会话：多数宿主只在启动时扫描 skill 目录。
2. 查看可用 skill 列表，确认出现 `deep-test`。
3. 在目标工程中发出触发语（如"深度测试这个工程"），确认能加载 `SKILL.md`，并能读到 `references/deep-procedure.md`。

### 更新

安装目录是独立副本，修改源目录不会自动同步。更新时重新复制 `SKILL.md` 与 `references/`，或在支持链接的宿主上改用符号链接。

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