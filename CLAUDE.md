# Web Content to Claude Code Converter

## Goal
Convert web content (coding practices, frameworks, etc.) into Claude Code instruction format.

## Problem
Creating Claude Code instructions manually requires multiple time-consuming steps:

- Collect knowledge from web
- Understand content
- Structure data manually
- Convert to markdown format

This project automates the process.

## Input/Output

### Input
- URL and settings in `./config.toml` > `input` section

### Output
- File saved to path in `./config.toml` > `output` section
- Filename format: `./out/RESULT_{title}.md`
  - Example: "golang async" → `./out/RESULT_golang_async.md`
  - Example: "swot analysis" → `./out/RESULT_swot_analysis.md`

## Workflow

### 1. Read Settings
- Read `./config.toml`
- If missing: Show error and tell user to run `cp config.toml.example config.toml`
- **Detect wheter url is single or multiple(pointed by file)**

### 2. Fetch Web Content
- (**Iterate below process with each urls if input is not single**)
- Use `mcp-fetch-server` to get content from URL
- If 404 Not Found: Show error and **stop**
- If fetch fails: Try `playwright-mcp`
- If both fail: Show error and **stop**
- Save fetched content to `./tmp/`

### 3. Analyze Content
- Analyze saved content of `./tmp/`
- Create 3 variants in `./tmp/{title}_{A|B|C}.md`:
  - **A variant**: Actionable (examples, code snippets)
  - **B variant**: Concise (minimal, dense)
  - **C variant**: Complete (comprehensive)
- **Important**: Restructure only. Do not transform or bias the original content.

### 4. Compare and Merge
- Compare A, B, C using `Comparison Criteria` below
- Merge using `Merge logic` below
- Output final file per `Output` section above
- Add source URL at end: `**ref-1. [Title](http://example.com)**`
- If `output > translation.enabled == true`: Output translated version too

### 5. Cleanup
- No specific cleanup needed

## Comparison Criteria

| Criterion | What to Check |
|-----------|--------------|
| Actionability | Has concrete examples and code |
| Conciseness | No redundancy, high density, simple |
| Structure | Clear hierarchy and flow |
| Completeness | Covers key source points |
| Claude Format | Proper markdown (lists, code, headers) |

## Merge Logic

1. Start with B (concise) as base
2. Add examples from A where needed for clarity
3. Fill gaps with C for completeness
4. Remove redundancy

## Notes
- `./config.toml.example` is a template. Do not edit it.
