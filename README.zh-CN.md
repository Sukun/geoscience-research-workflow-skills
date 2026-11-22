# 地学科研工作流 Skills（中文说明）

[English README](README.md)

作者：Sukun Cheng（[@Sukun](https://github.com/Sukun)）

一组用于计算型地学研究的 agent skills：从计划、执行、整理结果到做幻灯片，每一步都有明确边界。面向 Python / xarray / NetCDF 项目，可在 Claude Code、Codex 和支持 agent skills 的 VS Code 插件中使用。每个 skill 是一个带 `SKILL.md` 的文件夹，用 `/<skill-name>` 调用。

本页是简短导航，详细规则以英文 README 和各 `SKILL.md` 为准。

## 设计原则

- 计划先写成需要你批准的 `TASK_CONTRACT.md`，列明不做的事（Non-Goals）和工作量上限（Scope Budget）。
- 执行只接受已批准的合同，不擅自修补、扩展、重构、加图或解释结果。
- 科学解释放在验证之后，由 `4c-report` 单独完成。
- 幻灯片从已批准的分镜机械渲染；在 PowerPoint 里手工调整的内容可以读回分镜和版面文件。

## 流程

```
项目整理    init-project-structure · 0-explore-data · notes-organizer
执行        1-plan-task → [2-review-plan] → 3-conduct-plan
结果        4a-quickview → 4b-topic-record；4c-report（验证后的科学解释）
幻灯片      5-ppt-design → 6-ppt-create → 7-ppt-to-5（手工修改后读回）
记录        noteit
```

| Skill | 作用 |
|---|---|
| `init-project-structure` | 整理项目文档：README、大纲、roadmap |
| `0-explore-data` | 只读地查看项目、数据和已有结果 |
| `notes-organizer` | 维护 `RESEARCH_NOTES.md` 和分主题笔记 |
| `1-plan-task` | 把请求写成待批准的任务合同 |
| `2-review-plan` | 可选：对高风险方法或计算做审稿式审查 |
| `3-conduct-plan` | 按已批准合同执行，只记录事实 |
| `4a-quickview` | 把已有图整理成图为主的速览，可直接出 PPT |
| `4b-topic-record` | 按主题汇总结果，生成 `00_README.md` |
| `4c-report` | 明确要求时，写科学报告或解释 |
| `5-ppt-design` | 决定幻灯片故事线，写分镜 |
| `6-ppt-create` | 把分镜渲染成 PPTX（python-pptx，无需 Pandoc） |
| `7-ppt-to-5` | 把手工改过的 PPT 读回分镜和版面文件 |
| `noteit` | 按要求记录一条研究心得 |

## 安装

```bash
git clone https://github.com/Sukun/geoscience-research-workflow-skills.git
```

把 `skills/` 下需要的文件夹链接或复制到客户端的 skills 目录：Claude Code 常用 `~/.claude/skills/`，Codex 常用 `~/.agents/skills/`。保持文件夹完整，`SKILL.md` 用相对路径引用自带的脚本和参考文件。

做幻灯片需要 Python 的 `python-pptx` 和 Pillow；跑测试需要 `pytest`。

## 本地检查

```bash
python3 tools/verify_skill_tree.py skills
python3 -m pytest skills/6-ppt-create/tests skills/7-ppt-to-5/tests
```

## 示例

[合成降水趋势示例](examples/demo-precip-trend/README.md)展示了合同、执行记录和后续解释如何分开。数据是虚构的。

## 许可

MIT © Sukun Cheng，见 [LICENSE](LICENSE)。
