# HR Recruiting Screening Skill

A portable AI Agent skill for HR teams, hiring managers, and technical interviewers.

It screens PDF resumes against a job description, ranks candidates with a 100-point scoring model, explains candidate fit, and generates tailored interview focus areas, questions, reference answer points, follow-up probes, and risk signals.

The default README is Chinese. This English version is provided for cross-team and open-source usage.

## Use Cases

- Screen PDF resumes against a JD
- Rank candidates with a 100-point score
- Explain candidate strengths, weaknesses, risks, and recommendations
- Consider role fit, education, major, company background, stability, and project quality
- Generate interview focus areas, interview questions, reference answer points, and follow-up probes
- Turn hiring screening into a reusable AI Agent workflow

## Features

The first version supports:

- Batch PDF resume text extraction
- Recursive scanning of one or more resume folders
- JD-driven candidate ranking
- Candidate detail reports
- A configurable 100-point scoring model
- Interview question and reference answer templates
- Markdown / JSON / TXT outputs

It does not:

- Replace final hiring decisions
- Contact candidates automatically
- Perform OCR for image-only PDFs
- Make discriminatory decisions based on protected attributes

## Repository Structure

```text
.
├── SKILL.md
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

## Quick Start

### 1. Install The Skill

If your agent environment supports the `skills` CLI, install it with:

```bash
npx skills add huajiexiewenfeng/hr-recruiting-screening-skill
```

You can also install it manually by placing this repository under a project skill directory, for example:

```text
.agents/skills/hr-recruiting-screening/
```

### 2. Install Script Dependencies

```bash
pip install -r scripts/requirements.txt
```

Skip this step if `pypdf` is already available in your environment.

### 3. Extract Text From PDF Resumes

Single folder:

```bash
python scripts/extract_resumes.py --input "D:/workspace/ai-workspace/jd" --output output/hr-resume-extracts
```

Multiple folders:

```bash
python scripts/extract_resumes.py --input "D:/resumes/backend" "D:/resumes/ai" --output output/hr-resume-extracts
```

The script writes:

```text
output/hr-resume-extracts/
├── resumes.json
├── extraction-summary.md
├── CandidateA.txt
├── CandidateB.txt
└── ...
```

### 4. Ask Your AI Agent To Use The Skill

Example prompt:

```text
Use the hr-recruiting-screening skill. Based on this JD and the extracted resumes in output/hr-resume-extracts, rank candidates, score them out of 100, recommend interview candidates, and explain the scoring reasons.
```

Interview generation:

```text
Use the hr-recruiting-screening skill. For selected candidates A, B, and C, generate interview focus areas, interview questions, reference answer points, follow-up probes, and rejection signals.
```

## Scoring Model

Default total score: 100 points.

```text
1. Role match: 40
2. Technical/professional depth: 20
3. Education and major: 10
4. Stability: 10
5. Project and company quality: 10
6. Risk control: 10
```

See:

[references/scoring-rubric.md](references/scoring-rubric.md)

## Screening Report

A screening report should include:

- Candidate ranking table
- Score breakdown
- Recommended interview list
- Non-prioritized candidates
- Strengths, weaknesses, and risks for each candidate
- Interview verification points
- Scoring assumptions and missing resume information

Template:

[references/report-template.md](references/report-template.md)

## Interview Question Generation

Interview questions should validate the JD and resume-specific claims. They are not generic textbook questions.

Each candidate plan should include:

- Candidate positioning
- Interview focus areas
- Questions
- Why each question matters
- Reference answer points
- Follow-up probes
- Weak answer signals
- Risk signals

Template:

[references/interview-template.md](references/interview-template.md)

## Design Principles

### 1. Do Not Rely Only On Keywords

Candidate ranking should not be pure keyword matching. A candidate with fewer keywords but deeper ownership and stronger role relevance may be a better interview target.

### 2. Treat Resume Risks As Verification Points

Outsourcing background, short tenures, vague project descriptions, and cross-domain transitions should be verified in interviews, not treated as automatic rejection reasons.

### 3. Separate Facts From Judgments

Reports should distinguish:

- Facts explicitly stated in the resume
- Reasoned judgments based on those facts
- Questions that must be verified in interviews

### 4. Avoid Improper Hiring Judgments

Do not screen based on protected attributes such as gender, ethnicity, marital status, childbirth status, or household registration. Age should only be discussed when the user explicitly asks, and only as career-stage or role-level fit, not as a discriminatory rejection factor.

## Cross-Agent Usage

### Codex

Place this repository under `.agents/skills/hr-recruiting-screening/`, or let Codex read this repository's `SKILL.md`.

### Cursor

Use `SKILL.md`, `references/`, and `scripts/` as project rules and tools.

### WorkBuddy Or Other Agents

Any agent that can read Markdown and run Python scripts can follow the workflow in `SKILL.md`.

## Examples

Sample JD:

[examples/sample-jd.md](examples/sample-jd.md)

Sample output:

[examples/sample-output.md](examples/sample-output.md)

## Roadmap

- DOCX resume extraction
- OCR for image-only PDFs
- Excel export for ranking reports
- Role-specific scoring presets
- Interview record feedback
- Score calibration from historical hiring outcomes

## License

MIT

