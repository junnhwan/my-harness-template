# Harness for long-running development

本模板参考 Anthropic 文章《Harness design for long-running application development》的核心机制，落地为一个可手工执行、也可后续自动化的三代理协作工作流。

核心设计：
- `Planner`：把简短需求扩展为高层产品规格与分 Sprint 待办
- `Generator`：按 Sprint 合同逐轮实现
- `Evaluator`：独立审合同、独立验收、硬门槛判定通过/失败
- `artifacts/`：所有协作通过工件文件完成，而不是只靠聊天上下文

## 目录说明
- `AGENTS.md`：工作流总规则
- `agents/`：三位代理的中文提示词
- `artifacts/`：规格、合同、构建报告、QA 报告、评分标准等工件模板
- `context/`：用户需求、项目背景等输入上下文
- `logs/`：运行记录与迭代历史
- `scripts/`：可选脚本占位（本模板暂不强依赖脚本）

## 最小运行流程
1. 把需求写入 `context/product_request.md`
2. 使用 `agents/planner.md` 运行 Planner，生成：
   - `artifacts/product_spec.md`
   - `artifacts/sprint_backlog.md`
3. 使用 `agents/generator.md` 运行 Generator，先生成：
   - `artifacts/sprint_contract.md`
4. 使用 `agents/evaluator.md` 运行 Evaluator，审查合同：
   - 输出到 `artifacts/contract_review.md`
5. 合同通过后，让 Generator 进行实现，并写：
   - `artifacts/build_report.md`
6. 再让 Evaluator 做 QA，输出：
   - `artifacts/qa_report.md`
7. 如果 `qa_report.md` 结论为 `FAIL`，回到 Generator 修复
8. 如果通过，推进下一个 Sprint

## 强约束
- 不允许跳过 `Sprint Contract`
- 不允许 Generator 自己宣布“已完成”而不经过 Evaluator
- Evaluator 必须按 `artifacts/eval_criteria.md` 的硬阈值判定
- 任一评分项低于阈值，则 Sprint 失败
- `MEMORY.md` 不是本模板必需项；如需引入，应在 `AGENTS.md` 中显式声明使用方式

## 推荐用法
- 先手工跑通 1~2 个 Sprint，再考虑自动化
- 先保持每轮改动小而明确，再逐渐放大单轮范围
- 所有阶段性决策都写入工件，而不是只留在对话里
