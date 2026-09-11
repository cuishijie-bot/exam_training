# 公务员考试训练管理系统

这是一个以GitHub为共享数据源的低摩擦训练系统：

```text
用户负责学习执行
→ Codex负责计划、记录、结算和Git同步
→ GitHub保存共享数据
→ ChatGPT读取GitHub进行长期监督、复盘和纠偏
```

## 当前训练方式

- 阶段：低摩擦知识打底期。
- 单元：一个知识点 + 一组5题。
- 工作日：学1练5，有余力才做第二组。
- KPI：闭环完成 → 能识别并口述方法 → 正确率。
- 保留A/B/C降级，不补课，不制造训练债务。

详细训练规则见 [AGENTS.md](AGENTS.md)，执行红线摘要见 [RULES.md](RULES.md)。

## 三方分工

### 用户

只负责学习、做题，以及补充Codex无法自动读取的主观状态或必要缺失数据。不需要手工填写日志、CSV、计算正确率，也不需要向ChatGPT重复汇报已经推送的数据。

### Codex

负责：

- 维护 `TODAY.md`；
- 生成 `logs/YYYY-MM-DD.md`；
- 更新 `stats/training.csv`；
- 维护必要错题与知识进度；
- 训练结算；
- Git commit和push。

### ChatGPT

通过GitHub读取 `TODAY.md`、`CURRENT_WEEK.md`、`logs/`、`stats/training.csv`，必要时读取规则文件，用于监督计划与实际执行、识别重复拖延、判断训练是否过重并调整行为策略。

## 唯一数据链路

本项目不建立第二套监督数据库：

```text
TODAY.md
  ↓ 结算
logs/YYYY-MM-DD.md
  ↓ 汇总
stats/training.csv
  ↓ commit + push
GitHub
  ↓
ChatGPT监督
```

- `TODAY.md`：当天计划、执行中进度、结算结果。
- `logs/`：每天完整上下文和行为证据。
- `stats/training.csv`：长期结构化分析。
- `CURRENT_WEEK.md`：当前阶段、知识进度和本周安排。
- `wrongbook/`：少量真正值得长期复习的问题。

本地文件更新不等于同步完成。只有commit并成功push到 `origin/main` 后，ChatGPT才能读取最新监督数据。

## 每天怎么用

早上或公交：直接打开 [TODAY.md](TODAY.md) 执行。

训练中：正常告诉Codex答题结果即可；已经写入文件或当前对话的信息不会重复询问。

训练结束：只发送：

```text
训练结算
```

Codex自动执行：

```text
读取已有数据
→ 只询问缺失项
→ 填写TODAY训练结果
→ 更新logs和stats
→ 必要时更新wrongbook
→ 判断知识点推进
→ 根据情况生成次日计划
→ 检查diff
→ git commit
→ git pull --rebase origin main
→ git push origin main
```

如果用户已明确取消次日计划，结算时不自动生成。

## 结算数据规则

- 没有发生的项目写“无”。
- 不知道的数据写“未报告”。
- 不猜测题数、正确数、时间、方法口述、拖延或未完成原因。
- 宁可记录部分完成或未完成，也不美化完成率。
- 同一问题在最近7～14天至少出现3次，才标记为重复行为模式。

## Git安全

- 只使用当前 `main`。
- 禁止 `git push --force`。
- 禁止 `git reset --hard origin/main`。
- 不提交无关用户修改，不删除既有训练数据。
- pull产生冲突时停止并报告，不自动覆盖。
- push失败时明确说明：本地记录已保存，但GitHub尚未同步，ChatGPT暂时读取不到最新数据。

更简洁的个人操作说明见 [exam_traning_每日学习操作指南.md](exam_traning_每日学习操作指南.md)。
