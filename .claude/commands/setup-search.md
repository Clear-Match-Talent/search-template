# Setup Search

You are helping a recruiter set up a new search repo with the required source documents. Walk them through each step below. Be direct and clear — tell them exactly what to do.

## Step 1: Confirm source documents

Ask the user to provide the following three documents for this search. They can paste content directly, provide file paths, or upload files:

1. **Job Description (JD)** — the full job description for the role
2. **Intake Call Notes** — transcript or notes from the intake call with the hiring manager
3. **Ideal Candidate Profiles** (optional) — example LinkedIn profiles, resumes, or descriptions of ideal candidates. There may be multiple.
4. **Additional Documents** (optional) — any other relevant materials (company overview, team structure, comp bands, hiring manager preferences, etc.)

Tell them: "I need the source documents for this search. Paste them here, give me file paths, or upload them. At minimum I need the JD and intake notes. Ideal candidate profiles and any other relevant docs are optional but help a lot. You can provide as many as you have."

Wait for the user to provide documents before proceeding.

## Step 2: Save source documents

Save each document the user provides to the correct location:

- JD → `source_docs/jd.md`
- Intake call notes → `source_docs/intake_notes.md`
- Ideal candidate profiles → `source_docs/ideal_profiles/` directory, one file per profile (e.g., `profile_1.md`, `profile_jane_doe.md`). If only one profile, save as `source_docs/ideal_profiles.md`.
- Additional documents → `source_docs/` with a descriptive filename (e.g., `source_docs/comp_bands.md`, `source_docs/team_structure.md`)

When saving, preserve the original content. Add a markdown title at the top if one isn't present (e.g., `# Job Description`).

## Step 3: Confirm and summarize

After all documents are saved, give the user a brief summary:
- List which files were saved and where
- Note if ideal profiles or additional docs were skipped (that's fine)
- Tell them: "Source docs are set up. Run `/generate-rubric` next to create the scoring rubric."

## Important

- Do NOT generate the rubric in this step. That's a separate skill.
- Do NOT run any pipeline commands.
- If a document is missing or unclear, ask the user to clarify before saving.
- If the user provides extra context about the role or client in conversation (not as a document), save it as additional notes at the bottom of intake_notes.md.
