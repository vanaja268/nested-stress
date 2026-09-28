# CodeScan XML stress fixtures — 250K lines / 2K target findings per file

This package contains syntactically valid XML fixtures intended for scanner/parser stress testing.
Each profile file has exactly **250,000 physical lines** and exactly **2,000 `<readable>true</readable>` nodes**.

## Files

- `QA_Wide_125K_Siblings.profile` — 124,998 sibling `<fieldPermissions>` elements under the Profile root. The first 2,000 use `readable=true`; all remaining siblings use `readable=false`. This is the heap/node-list allocation stress case and contains no padding comments.
- `QA_Deep_100_Levels.profile` — one uninterrupted 100-level synthetic nesting chain.
- `QA_Deep_200_Levels.profile` — one uninterrupted 200-level synthetic nesting chain.
- `QA_Deep_500_Levels.profile` — one uninterrupted 500-level synthetic nesting chain.

The deep files place all 2,000 `fieldPermissions` blocks **inside the deepest level**. Opening tags are consecutive, and closing tags are consecutive in reverse order; no padding/comments interrupt the nesting chain. Remaining lines are `<qaPayload>` elements at the deepest level so the files stay large without breaking the nesting structure.

## XPath for an exact 2,000-finding calibration

For CodeScan's SFMeta XPath representation, use only the rule equivalent to:

`//fieldPermissions/readable/text[@Image="true"]`

If testing with a normal XML XPath engine, the equivalent is:

`//*[local-name()="fieldPermissions"]/*[local-name()="readable" and text()="true"]`

Enable only this calibration rule when you need the expected count to remain exactly 2,000. Other XPath rules such as matching every `<field>` or every `<fieldPermissions>` node will naturally produce more findings, especially in the 125K-wide file.

## Important note

The wide file uses ordinary Salesforce Profile-style `fieldPermissions` siblings. The deep files intentionally add synthetic `QA_Nest_*` / `qaPayload` elements because Salesforce Profile metadata does not naturally provide 100–500 levels of legal schema nesting. They are well-formed XML for parser/CodeScan robustness testing, but they are not intended for Salesforce deployment.

See `qa_manifest.csv` for validated line counts, element counts, parser depth, file size, and SHA-256 checksums.
