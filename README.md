# HR Recruiting Screening Skill

简体中文 | [English](./README.en.md)

一个面向 HR、招聘负责人和技术面试官的通用 AI Agent 招聘筛选技能包。

它包含三个独立技能：简历筛选排序、人员明细报告、面试题生成。可以根据岗位 JD 批量分析 PDF 简历，按 100 分制给候选人排序，解释得分原因，并为选中的候选人生成定制化面试重点、面试题、参考答案、追问和风险信号。

这个技能不绑定特定 Agent，可用于 Codex、Cursor、WorkBuddy 或其他支持读取 Markdown 技能文件和运行 Python 脚本的 AI 工作流。

## 适用场景

- 根据 JD 批量筛选 PDF 简历
- 对候选人进行 100 分制评分和排序
- 输出候选人优点、缺点、风险点和推荐结论
- 分析学历、专业、公司背景、工作稳定度和项目含金量
- 为已选面试人生成面试重点、面试题、参考答案和追问
- 将招聘筛选流程沉淀成可复用的 AI Agent 技能

## 能力范围

当前第一版支持：

- PDF 简历批量文本抽取
- 一个或多个简历文件夹递归扫描
- JD 驱动的候选人排序
- 候选人明细分析
- 100 分评分模型
- 面试题和参考答案生成模板
- Markdown / JSON / TXT 输出

不直接做的事情：

- 不替代最终招聘决策
- 不自动联系候选人
- 不做 OCR 图片简历识别
- 不根据受保护身份特征做歧视性判断

## 目录结构

```text
.
├── skills/
│   ├── hr-resume-screening/
│   │   └── SKILL.md
│   ├── hr-candidate-detail-report/
│   │   └── SKILL.md
│   └── hr-interview-question-generator/
│       └── SKILL.md
├── scripts/
│   ├── extract_resumes.py
│   └── requirements.txt
├── references/
│   ├── scoring-rubric.md
│   ├── report-template.md
│   └── interview-template.md
└── examples/
    ├── sample-jd.md
    └── sample-output.md
```

## 三个技能

### `hr-resume-screening`

用于第一轮筛选：读取 JD 和简历，批量分析 PDF，输出候选人 100 分制排序、推荐面试名单和初步风险点。

### `hr-candidate-detail-report`

用于人员明细报告：解释每位候选人的得分原因、优势、短板、学历专业、公司背景、稳定度和面试验证点。

### `hr-interview-question-generator`

用于面试准备：针对已选候选人生成定制化面试重点、面试题、参考答案、追问和淘汰信号。

## 快速开始

### 1. 安装 Skill

如果你的 Agent 环境支持 `skills` CLI，可以直接安装：

```bash
npx skills add huajiexiewenfeng/hr-recruiting-screening-skill
```

如果需要安装到当前项目，也可以手动把本仓库放到项目的技能目录，例如：

```text
.agents/skills/
  hr-resume-screening/
  hr-candidate-detail-report/
  hr-interview-question-generator/
```

### 2. 安装脚本依赖

```bash
pip install -r scripts/requirements.txt
```

如果你的环境已经安装了 `pypdf`，可以跳过这一步。

### 3. 抽取 PDF 简历文本

单个文件夹：

```bash
python scripts/extract_resumes.py --input "D:/workspace/ai-workspace/jd" --output output/hr-resume-extracts
```

多个文件夹：

```bash
python scripts/extract_resumes.py --input "D:/resumes/backend" "D:/resumes/ai" --output output/hr-resume-extracts
```

脚本会生成：

```text
output/hr-resume-extracts/
├── resumes.json
├── extraction-summary.md
├── 候选人A.txt
├── 候选人B.txt
└── ...
```

### 4. 让 AI Agent 使用技能分析

把 JD 和抽取结果交给 Agent，并要求使用本技能。例如：

```text
请使用 hr-resume-screening 技能，根据这个 JD 和 output/hr-resume-extracts 里的简历抽取结果，对候选人排序，输出 100 分制评分和推荐面试名单。
```

生成人员明细：

```text
请使用 hr-candidate-detail-report 技能，解释这些候选人的得分原因、优势、短板、风险点和面试验证重点。
```

生成面试题：

```text
请使用 hr-interview-question-generator 技能，针对已选候选人 A、B、C，生成面试重点、面试题、参考答案、追问和淘汰信号。
```

## 评分模型

默认总分为 100 分：

```text
1. 岗位匹配度：40 分
2. 技术/专业能力深度：20 分
3. 学历与专业：10 分
4. 工作稳定度：10 分
5. 项目与公司含金量：10 分
6. 风险控制：10 分
```

详细规则见：

[references/scoring-rubric.md](references/scoring-rubric.md)

## 输出报告

筛选报告建议包含：

- 候选人排序总表
- 分项得分表
- 推荐面试名单
- 不优先候选人
- 每个人的优势、短板、风险点
- 面试验证重点
- 评分假设和简历信息缺口

报告模板见：

[references/report-template.md](references/report-template.md)

## 面试题生成

面试题不是通用八股题，而是围绕 JD 和候选人简历中的关键声明做定向验证。

每个候选人的面试计划建议包含：

- 候选人定位
- 面试重点
- 面试题
- 为什么问这个问题
- 参考答案要点
- 追问
- 弱回答信号
- 风险信号

模板见：

[references/interview-template.md](references/interview-template.md)

## 设计原则

### 1. 不只做关键词匹配

候选人排序不能只看关键词数量。一个关键词少但项目职责真实、业务贴合、能讲清系统取舍的人，可能比关键词堆得多但经历浅的人更适合面试。

### 2. 简历风险是验证点，不是自动淘汰理由

外包、短工作经历、项目描述模糊、跨行业背景都应该作为面试验证点，而不是自动否决。

### 3. 区分事实和判断

报告里应区分：

- 简历中明确写到的事实
- 基于事实做出的判断
- 需要面试确认的问题

### 4. 避免不当招聘判断

不要根据性别、民族、婚育、户籍等受保护身份特征做筛选判断。年龄只应在用户明确要求时作为职业阶段和岗位层级匹配的辅助讨论点，不能作为歧视性淘汰理由。

## 跨工具使用说明

### Codex

把本仓库的 `skills/` 下三个技能复制到项目的 `.agents/skills/` 目录，或通过 `npx skills add huajiexiewenfeng/hr-recruiting-screening-skill` 安装。

### Cursor

可以将 `skills/`、`references/` 和 `scripts/` 放入项目中，作为规则文档和工具脚本使用。

### WorkBuddy 或其他 Agent

只要 Agent 能读取 Markdown 文档并运行 Python 脚本，即可按三个 `SKILL.md` 中的流程使用。

## 示例 JD

见：

[examples/sample-jd.md](examples/sample-jd.md)

## 示例输出

见：

[examples/sample-output.md](examples/sample-output.md)

## 后续计划

- 支持 DOCX 简历抽取
- 支持 OCR 图片 PDF
- 支持导出 Excel 排序表
- 支持岗位类型预设评分权重
- 支持候选人面试记录回填
- 支持历史面试结果校准评分模型

## License

MIT
