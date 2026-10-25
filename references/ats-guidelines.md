# ATS and CV writing guidelines

## What the renderer already guarantees

`jh render` produces a single-column document with a real text layer, standard fonts, no tables, text boxes, images, icons or skill bars, contact details in the body (not in a page header), and consistent entry layout (title and dates on one line, organisation and location on the next). Do not try to fix formatting by changing content. Put every fact in the section where an ATS expects it.

## Section order

| Candidate | Order |
| --- | --- |
| Experienced (2+ years) | Summary, Experience, Skills, Education, Certifications, Projects, Languages, Additional |
| Early-career or graduate | Summary, Education, Experience (including internships), Projects, Skills, Certifications, Languages |
| Career changer | Summary, Skills, Relevant experience or Projects, Other experience, Education |

Drop empty sections. Market conventions (see `regional-conventions.md`) can add sections, such as job intention for CN or referees for AU.

## Standard section titles

ATS parsers look for standard headings. Use these in the CV language:

| Language | Summary | Experience | Education | Skills | Certifications | Projects | Languages |
| --- | --- | --- | --- | --- | --- | --- | --- |
| en | Professional Summary | Experience | Education | Skills | Certifications | Projects | Languages |
| zh-Hant | 個人簡介 | 工作經驗 | 教育背景 | 專業技能 | 專業證書 | 項目經驗 (TW: 專案經驗) | 語言能力 |
| zh-Hans | 个人简介 | 工作经历 | 教育背景 | 专业技能 | 证书 | 项目经历 | 语言能力 |
| ja | 職務要約 | 職務経歴 | 学歴 | 活かせる経験・知識・技術 | 資格 | プロジェクト | 語学 |
| ko | 요약 | 경력 사항 | 학력 | 보유 기술 | 자격증 | 프로젝트 | 어학 |
| de | Profil | Berufserfahrung | Ausbildung | Kenntnisse | Zertifikate | Projekte | Sprachen |
| fr | Profil | Expérience professionnelle | Formation | Compétences | Certifications | Projets | Langues |
| es | Perfil profesional | Experiencia profesional | Formación | Competencias | Certificaciones | Proyectos | Idiomas |
| pt | Resumo profissional | Experiência profissional | Formação acadêmica | Competências | Certificações | Projetos | Idiomas |
| it | Profilo | Esperienza professionale | Istruzione e formazione | Competenze | Certificazioni | Progetti | Lingue |
| nl | Profiel | Werkervaring | Opleiding | Vaardigheden | Certificaten | Projecten | Talen |

## Writing achievement bullets

- Pattern: action verb + what you did + measurable result or scope. Example: "Cut onboarding time from 5 days to 1 day by rebuilding the merchant sign-up flow with a squad of 11."
- Lead with the result when it is strong.
- Use numbers the user confirmed: money, %, time, volume, team size, users, rank. Never estimate a number on the user's behalf.
- Past tense for past roles, present tense for the current role. No first-person pronouns in English CVs.
- Keep each bullet to 1–2 lines. Use 3–6 bullets for recent relevant roles and 1–2 for older ones.
- Avoid filler ("responsible for", "various", "helped with") and unproven soft-skill claims ("hard-working", "team player").

## Keyword strategy

1. Extract the posting's terms exactly as written: hard skills, tools, methods, certifications, domain terms, and the job title. Mark each as `must` or `nice`.
2. Where the profile has evidence, use the posting's exact wording in the bullet. If the posting says "stakeholder management" and the profile says "worked with Compliance", write "Managed stakeholders across Compliance and Risk…".
3. Spell out an acronym once with its expansion when the posting uses only one form: "Know Your Customer (KYC)".
4. Place critical keywords in context in Experience bullets, not only in the Skills list. Mention them in the Summary when truthful.
5. Do not repeat a keyword more than about three times. Never copy sentences from the posting.
6. Job titles: keep the real title. If it is unusual, you may add a plain equivalent in parentheses, such as "Growth Ninja (Marketing Manager)", when that is accurate. The headline may use the target title only if the candidate's experience genuinely matches it.

## Selecting content and fitting the length

- Rank achievements by relevance to the posting, then by impact, then by recency.
- Condense or drop roles older than about 10–15 years or unrelated to the target, but keep the timeline honest. Use a one-line "Earlier experience" entry rather than leaving an unexplained gap, and tell the user what you dropped.
- To fit the target length: drop the weakest bullets first, then condense older roles, then reduce `meta.fontSize` (not below 9.5).

## Honesty rules (non-negotiable)

- Use only facts that exist in `profile.json` or that the user confirmed in this conversation.
- Rephrasing and reordering are fine. Inflating scope, seniority, numbers, dates or titles is not.
- Never add a skill, tool, certification or employer the profile does not support. Missing requirements go into the gap list and are raised with the user.
- No hidden text, white text, keyword blocks, or invisible formatting tricks.
- Every entry, bullet and list item in `cv.json` carries `source` ids, and `jh validate cv` must pass. It catches invented names, dates and numbers, but not every exaggeration in wording, so reread each line against the facts it cites.

## File names and formats

- File name: `First_Last_Company_Role`, for example `Alex_Chan_ExampleBank_Senior_Product_Manager.pdf`. Use the name the candidate goes by.
- PDF is the default. Also produce DOCX when the posting asks for Word, or the user asks; some older ATS (for example Taleo) parse DOCX more reliably.

## Gap analysis

For each requirement in the posting:

| Evidence | Meaning | Action |
| --- | --- | --- |
| strong | Profile has a matching achievement or skill with evidence | Feature it, using the posting's wording |
| partial | Related but not the same (for example Looker vs Tableau) | Present the related evidence honestly; mention transferability in the summary or report |
| none | Nothing in the profile | Ask the user. If they confirm real experience, add it to the profile first; otherwise list it in the report as a gap |
