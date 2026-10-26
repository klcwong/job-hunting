# Job Hunting: a Claude Code and Codex plugin

Tailor an ATS-optimized CV to every job you apply for, following the hiring conventions of the job's country, and keep a history of every CV you send.

| Command | What it does |
| --- | --- |
| `/job-hunting:setup [cv.pdf or cv.docx]` | Imports your CV and asks which country or region you are job hunting in. Builds a master profile (`profile.json`) of everything you have done, after a short interview to add missing numbers and skills. |
| `/job-hunting:tailor <job URL or text>` | Reads the job posting, finds keyword gaps (and asks you about them), and writes a CV for that job from your profile. Renders it to PDF (add `--docx` or `--both` for Word), checks it the way an ATS reads it, and saves it with the job description. |
| `/job-hunting:cv-history [filter]` | Shows a table of every tailored CV: date and time, role, company, job description, CV file and status. It can update a record's status or export the history to HTML or CSV. |

In Codex, start the command with `$` instead of `/`, for example `$job-hunting:tailor`. See [Use with Codex](#use-with-codex).

## How it works

- **One source of truth.** `profile.json` holds your whole career, which can be longer than any CV. Each tailored CV selects and rephrases facts from it. There is no base CV document to keep in sync.
- **Country-aware.** Setup records your target market(s). Tailoring applies that market's conventions: paper size, length, photo, personal details, salary and notice period, work authorisation, date format and section titles. See [references/regional-conventions.md](references/regional-conventions.md).
- **Honest by design.** Every entry and bullet in a tailored CV cites the profile facts it came from. Before rendering, the plugin checks that job titles, organizations, dates, numbers, skills and other names in the CV appear in those facts, and asks you about requirements you have no evidence for instead of inventing them. It cannot judge every rephrasing, so read your CV before you send it.
- **ATS round-trip.** After rendering, the plugin extracts the text back out of the PDF and reports keyword coverage and parsing problems, the way an ATS would see the file.

## Requirements

- [Claude Code](https://code.claude.com) or [Codex](https://developers.openai.com/codex)
- [uv](https://docs.astral.sh/uv/getting-started/installation/). It installs Python and the plugin's packages (fpdf2, python-docx, pypdf, jsonschema) automatically on first use.
- Fonts: Arial (Windows and macOS) or Liberation Sans or DejaVu Sans (Linux). Chinese, Japanese and Korean CVs use the system CJK fonts (Microsoft JhengHei or YaHei, Yu Gothic, Malgun Gothic, PingFang, or Noto Sans CJK).

## Install

```bash
claude plugin marketplace add klcwong/job-hunting
```

```bash
claude plugin install job-hunting@job-hunting
```

Or, inside Claude Code: `/plugin marketplace add klcwong/job-hunting`, then `/plugin install job-hunting@job-hunting`.

When you enable the plugin, Claude Code asks for the **data folder**. The default is `~/job-hunting`. Choose a folder you back up. It is not deleted when you uninstall the plugin.

## Use with Codex

The same skills run in Codex. Install the plugin from this repository's marketplace:

```bash
codex plugin marketplace add klcwong/job-hunting
```

```bash
codex plugin add job-hunting@job-hunting
```

Or run the first command, then install the plugin from `/plugins` inside Codex. Start a new session afterwards. Tested with Codex CLI 0.164. Older versions, such as 0.120, cannot install a plugin that sits at the root of its marketplace repository.

Run the skills as `$job-hunting:setup`, `$job-hunting:tailor <job URL or text>` and `$job-hunting:cv-history`, or describe what you want. Setup only runs when you call it by name.

Differences from Claude Code:

- **Data folder.** Codex does not ask for one when you install the plugin. The plugin uses `~/job-hunting` unless you set the `JOB_HUNTING_DATA_DIR` environment variable, for example in `~/.codex/config.toml`:

  ```toml
  [shell_environment_policy]
  set = { JOB_HUNTING_DATA_DIR = "D:/Backup/job-hunting" }
  ```

- **Approvals.** Codex runs commands in its sandbox. The helper writes to your data folder, `uv` downloads packages on first use, and fetching a job posting needs the network, so Codex asks you to approve those commands, depending on your approval settings. The pre-approved helper commands in the skills apply to Claude Code only.

## Get started

- **First time:** run `/job-hunting:setup`. Claude imports your CV, then asks you a few questions: which country or region you are job hunting in, and any numbers or skills your CV is missing. It uses your answers to build your master profile (`profile.json`).
- **For each new job description:** run `/job-hunting:tailor` to write a CV tailored to that job.

## Your data

```
~/job-hunting/
├── profile.json        your master profile
├── sources/            copies of the CVs you imported
├── backups/            earlier versions of profile.json
└── applications/
    └── 2026-10-25_1430_examplebank_senior-product-manager/
        ├── application.json   history record (status, coverage, files)
        ├── jd.md              the job posting, verbatim
        ├── keywords.json      keywords taken from the posting
        ├── cv.json            the tailored CV content
        ├── report.md          keyword coverage, gaps and changes
        └── Alex_Chan_ExampleBank_Senior_Product_Manager.pdf
```

Everything stays on your computer. Like anything you do in Claude Code or Codex, your CV and the job postings are sent to the model (Claude, or OpenAI's model in Codex) to be processed. The plugin never sends them anywhere else. Formats are documented in [references/data-formats.md](references/data-formats.md).

## Limitations

- **Photos.** The renderer does not place photos. For markets where photos are customary, add one yourself.
- **Japan.** The plugin produces a 職務経歴書 (career history) or an English résumé. It does not fill the standardized 履歴書 (rirekisho) form.
- **Job postings behind a login or built with JavaScript** (for example LinkedIn) often cannot be fetched. Paste the text instead.
- **Scripts in PDFs.** PDFs support Latin, Greek, Cyrillic, Chinese, Japanese and Korean text. Arabic, Hebrew, Thai, Devanagari and other scripts that need right-to-left layout or text shaping are only supported in DOCX output. Emoji and symbols that no installed font contains are reported, so you can replace them.
- **Fonts on minimal Linux systems or containers.** Install Liberation Sans or DejaVu Sans, plus Noto Sans CJK for Chinese, Japanese or Korean. Without them, rendering stops with a list of the characters it cannot draw.
- **Regional conventions** are guidance, not legal advice. The job posting's own instructions always win.

## License

MIT. See [LICENSE](LICENSE).
