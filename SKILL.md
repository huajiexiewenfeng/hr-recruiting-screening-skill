---
name: hr-recruiting-screening
description: Use this skill whenever the user wants to screen resumes, rank candidates against a JD, analyze PDF resumes from one or more folders, score candidates out of 100, explain candidate fit, compare education/company/stability risks, or generate tailored interview questions, interview focus areas, reference answers, follow-up probes, and rejection signals for selected candidates. This skill is portable across Codex, Cursor, WorkBuddy, and other agents.
---

# HR Recruiting Screening

## Purpose

Help HR, hiring managers, and technical interviewers screen candidates against a job description with a repeatable, explainable process.

This skill supports three connected workflows:

1. Resume screening and ranking.
2. Candidate detail reports with score reasons.
3. Interview focus, questions, reference answers, follow-up probes, and risk signals for selected candidates.

## Portability Principles

Keep the workflow portable across coding agents and local environments.

- Use ordinary Markdown for instructions and reports.
- Use ordinary Python scripts for deterministic file extraction.
- Do not rely on Codex-only tools, Cursor-only tools, or WorkBuddy-only tools in the skill instructions.
- If a tool can read PDFs directly, it may do so; otherwise use `scripts/extract_resumes.py`.
- Keep subjective hiring judgments in the model analysis layer, not in the extraction script.

## Required References

Read these files as needed:

- `references/scoring-rubric.md` for the 100-point scoring model.
- `references/report-template.md` when producing resume ranking and candidate detail reports.
- `references/interview-template.md` when producing interview questions and reference answers.

## Inputs

Accept any of these input forms:

- A JD pasted in the conversation.
- A JD file path, preferably `.md` or `.txt`.
- One resume PDF file.
- One folder of resume PDFs.
- Multiple folders of resume PDFs.
- Previously extracted resume `.txt` or `.json` files.

If the JD is missing, ask for it before scoring. If resume sources are missing, ask for the folder or files.

## Workflow A: Resume Screening And Ranking

1. Read the JD and identify:
   - Role title and level.
   - Must-have requirements.
   - Nice-to-have requirements.
   - Business domain.
   - Deal breakers.
   - Hidden priorities implied by the user's wording.
2. Read or extract each resume.
   - If files are PDFs and text is not already available, run `scripts/extract_resumes.py`.
   - Use recursive folder scanning when the user provides folders.
3. For each candidate, extract:
   - Name.
   - Years of experience.
   - Age, if present.
   - Education level, school, and major.
   - Company history.
   - Each job segment duration.
   - Recent job duration.
   - Core technologies.
   - Business domain experience.
   - Project role and responsibility depth.
   - Risk signals and missing information.
4. Score candidates using `references/scoring-rubric.md`.
5. Produce the ranking report using `references/report-template.md`.
6. Clearly separate:
   - Known resume facts.
   - Reasoned judgments.
   - Questions to verify in interview.

## Workflow B: Candidate Detail Reports

Use this when the user asks for "人员明细", "得分原因", "详细分析", "优缺点", or asks why a candidate ranks where they do.

For each candidate, include:

- Overall score.
- Score by dimension.
- Key strengths.
- Key weaknesses.
- Job-fit reasoning.
- Education and major assessment.
- Company and project quality assessment.
- Stability assessment.
- Risk signals.
- Interview recommendation.
- Questions that must be verified.

Avoid overconfidence. If a resume claim is ambiguous, mark it as a verification point.

## Workflow C: Interview Plan For Selected Candidates

Use this when the user has selected interview candidates or asks for "面试题", "面试重点", "答案", "追问", or "淘汰信号".

For each selected candidate:

1. Start from the JD requirements.
2. Use the candidate's resume-specific claims to design targeted validation questions.
3. Include reference answer points, not one rigid answer.
4. Include follow-up probes that reveal real project depth.
5. Include risk signals and weak-answer signals.
6. Prefer scenario questions over memorized textbook questions.

Use `references/interview-template.md`.

## PDF Extraction Script

Use `scripts/extract_resumes.py` when PDF text needs to be extracted.

Example:

```bash
python scripts/extract_resumes.py --input "D:/workspace/ai-workspace/jd" --output output/hr-resume-extracts
```

Multiple inputs:

```bash
python scripts/extract_resumes.py --input "D:/resumes/backend" "D:/resumes/ai" --output output/hr-resume-extracts
```

The script writes:

- `*.txt` text extracts.
- `resumes.json` structured extraction metadata.
- `extraction-summary.md` quick review summary.

If `pypdf` is unavailable, install it in the current environment:

```bash
pip install pypdf
```

## Scoring Discipline

Do not score only by keyword matching. A candidate with fewer keywords but deeper matching project ownership may rank higher than a candidate with many shallow keywords.

When scoring:

- Penalize vague claims such as "participated in" when no ownership is clear.
- Reward concrete project responsibility, system design, production incidents, scale, and trade-off reasoning.
- Treat outsourcing, short job segments, and inflated project wording as risk signals, not automatic rejection.
- Age should never be used as an unsupported negative signal. Use it only when the user explicitly asks to consider age, and discuss it as role-level fit or career-stage fit, not as a protected-trait judgment.
- Avoid protected-class discrimination. Focus on job-related evidence: skills, experience, stability, project ownership, communication, and role expectations.

## Output Language

Use the user's language. If the user writes in Chinese, produce Chinese reports. If the user writes in English, produce English reports.

## Final Recommendation Style

Keep recommendations actionable:

- "Strongly recommend interview"
- "Recommend interview"
- "Backup interview"
- "Do not prioritize"
- "Reject for this role"

For each recommendation, state the main reason in one sentence.

