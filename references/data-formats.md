# Data formats

All files are UTF-8 JSON or Markdown inside the user's data folder. The JSON Schemas in `schemas/` are authoritative; `jh validate` checks them. Full examples live in `examples/`.

## Data folder layout

```
<data folder>/
├── profile.json            master profile, the single source of truth
├── sources/                copies of the CVs the user imported
├── backups/                earlier versions of profile.json
├── history.html / .csv     exports created by /job-hunting:cv-history
└── applications/
    └── 2026-10-25_1430_examplebank_senior-product-manager/
        ├── application.json    record shown in the history table
        ├── jd.md               the job posting, verbatim
        ├── keywords.json       keywords extracted from the posting
        ├── cv.json             the tailored CV content
        ├── report.md           coverage, gaps and changes
        └── Alex_Chan_ExampleBank_Senior_Product_Manager.pdf
```

## profile.json

A superset of the candidate's career. It can be longer than any CV, because tailoring selects from it.

| Field | Notes |
| --- | --- |
| `schemaVersion` | Always `1`. |
| `meta.version` | Integer, +1 on every saved change. Back up with `jh profile backup` before overwriting. |
| `targetMarkets[]` | `code` (ISO 3166-1 alpha-2, e.g. `HK`), `country`, `primary` (exactly one `true`), `cvLanguage` (BCP 47, e.g. `en-GB`, `zh-Hant`), `paper` (`A4` or `Letter`), `notes`. |
| `targetRoles[]` | Titles the user is aiming for. |
| `personal` | `fullName`, `displayName` (name used on CVs), `localName` (e.g. Chinese name), `email`, `phone`, `location` (city, country), `links[]` (`label`, `url`), `workAuthorization[]` (`code`, `status`), `regional` (market-specific extras the user chose to share, e.g. `noticePeriod`, `expectedSalary`). Never store ID numbers. |
| `summary` | General summary; tailoring rewrites it per job. |
| `experience[]`, `volunteering[]` | `id`, `title`, `organization`, `location`, `employmentType`, `start`, `end` (`null` = current), `context` (company size or industry, team), `achievements[]`. |
| `achievements[]` | `id`, `text` (improved, ATS-ready wording), `original` (wording from the imported CV, kept for traceability), `skills[]`, `confirmed` (`true` once the user confirmed the facts). |
| `education[]` | `id`, `institution`, `qualification`, `field`, `location`, `start`, `end`, `grade`, `highlights[]` (same shape as achievements). |
| `skills[]` | `id`, `name`, `category`, `level`, `years`, `evidence[]` (achievement ids that prove the skill). |
| `certifications[]`, `projects[]`, `languages[]`, `awards[]`, `publications[]`, `additional[]` | See the schema; each item has an `id`. |
| `preferences` | `targetPages`, `includePhoto`, `notes`. |

Rules:

- Ids are lowercase kebab-case and unique across the whole file: `exp-<org>`, `exp-<org>-<n>`, `edu-<school>`, `skill-<name>`, `cert-<name>`, `proj-<name>`, `lang-<code>`.
- Dates are `YYYY-MM`, or `YYYY` when the month is unknown.
- Write content in the primary market's CV language. Proper nouns stay as the user writes them.

## cv.json

Render-ready content of one tailored CV. The renderer applies the layout; you only decide content and order.

```json
{
  "schemaVersion": 1,
  "meta": { "language": "en-GB", "paper": "A4", "market": "HK", "targetPages": 2, "fontSize": 10.5, "title": "Alex Chan - Senior Product Manager" },
  "header": {
    "name": "Alex Chan",
    "headline": "Senior Product Manager | Payments",
    "contact": ["alex.chan@example.com", "+852 5555 0123", "Hong Kong", "linkedin.com/in/alexchan-example"],
    "details": ["Hong Kong permanent resident", "Notice period: 1 month"]
  },
  "sections": [
    { "kind": "text", "title": "Professional Summary", "text": "…" },
    { "kind": "entries", "title": "Experience", "entries": [
      { "heading": "Senior Product Manager", "subheading": "PayFlow Ltd", "location": "Hong Kong",
        "dates": "Apr 2022 – Present", "source": ["exp-payflow"],
        "bullets": [{ "text": "…", "source": ["exp-payflow-2"] }] }
    ]},
    { "kind": "groups", "title": "Skills", "groups": [{ "label": "Data", "items": ["SQL", "Looker"] }] },
    { "kind": "list", "title": "Certifications", "items": [{ "text": "CSPO, Scrum Alliance, 2021", "source": ["cert-cspo"] }] }
  ]
}
```

| Section `kind` | Content | Use for |
| --- | --- | --- |
| `text` | `text` (newlines start new paragraphs) | Summary, job intention, short statements |
| `entries` | `entries[]`: `heading`, `subheading`, `location`, `dates`, `summary`, `bullets[]`, `source` | Experience, education, projects, volunteering |
| `groups` | `groups[]`: optional `label` and `items[]` (rendered as "Label: a, b, c") | Skills, languages, tools |
| `list` | `items[]` (bullets) | Certifications, awards, publications, additional |

Rules:

- `meta.language` is the CV language (BCP 47). `meta.market` is the market whose conventions you applied. `meta.paper` and `meta.targetPages` come from those conventions or from `profile.preferences`.
- `header.details` holds market-specific lines such as work authorisation, notice period or expected salary. Leave it out when the market does not expect them.
- Format dates for the CV language and market, e.g. "Apr 2022 – Present", "2022.04 – 至今", "04/2022 – heute".
- `meta.fontSize` (9–12, default 10.5) is the last lever for fitting the page target.
- Sections with no content are skipped when rendering.

Traceability rules, enforced by `jh validate cv --profile`:

| Part of the CV | Rule |
| --- | --- |
| Entry | `source` holds the id of the top-level profile item it shows (`exp-…`, `edu-…`, `proj-…`). The organization or school and the job title must appear in `heading`, `subheading` or `location`. The years in `dates` must match the item's dates. |
| Bullet | `source` cites that entry's item or its achievements. Every number must appear in the cited facts. |
| List item | `source` cites a profile item, and the text names it (e.g. the certification name). Numbers must appear in the cited facts. |
| Group item | A skill, language or certification name from the profile. Details in brackets must come from that profile item: "English (fluent)", "Python (advanced)". |
| Summary, headline, details | Numbers must appear in the profile or follow from the experience dates (years of experience). Capitalized names must appear in the profile. |
| Header | The name matches the profile. Phone numbers and other contact digits match `personal`. |

When `meta.language` differs from the profile's primary `cvLanguage`, wording checks only warn; numbers, dates and ids are still errors.

## keywords.json

```json
[
  { "term": "stakeholder management", "aliases": ["managed stakeholders"], "priority": "must" },
  { "term": "Kubernetes", "aliases": ["K8s"], "priority": "nice" }
]
```

`jh check` reports which terms (or aliases) appear in the rendered file.

## application.json

Created by `jh app new`, updated by `jh render` (file names, pages) and `jh app set` (everything else).

| Field | Notes |
| --- | --- |
| `id` | Folder name. |
| `createdAt` | ISO 8601 with time zone offset. |
| `company`, `role`, `location`, `country` | From the posting. `country` is an ISO code. |
| `jdUrl` | Posting URL, if any. |
| `jdSummary` | One line, at most 120 characters: the role's focus and key requirements. Shown in the history table. |
| `status` | `tailored`, `applied`, `interviewing`, `offer`, `rejected` or `withdrawn`. `statusHistory[]` records changes. |
| `keywordCoverage` | Fraction from `jh check` (`coverage.ratio`). |
| `profileVersion` | `profile.json` version used. |
| `files` | `jd`, `cv`, `keywords`, `report`, `pdf`, `docx` file names inside the folder. |
| `notes` | Free text from the user. |
