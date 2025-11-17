=====================================================================================================================
PHASE 0 — BUSINESS ANALYSIS
=====================================================================================================================

0.1  BUSINESS TEAM  
        ↓
0.2  Confluence SPEC (Draft)
        - Problem Statement
        - Goals
        - Business Rules
        - User Flow / Wireframes
        - Business Test Cases (BTC)
        - No TBD / No missing sections
        ↓
0.3  BUSINESS SIGN-OFF
        ↓

=====================================================================================================================
PHASE 1 — AI-AUGMENTED PRE-DEVELOPMENT PIPELINE
=====================================================================================================================

1.1  AI SPEC QUALITY ANALYZER  
        - Check completeness  
        - Validate BTC  
        - Detect missing sections  
        - Template compliance  
        - Pass / Fail  
            │
            ├── 1.2 SPEC NOT READY → AI requests fixes  
            └── 1.3 SPEC QUALITY PASSED  
                     ↓

1.4  AI CREATES EPIC (DRAFT)  
        - Summary  
        - Business value  
        - Scope / Non-scope  
        - Link Confluence SPEC  
        - Draft acceptance notes  
        ↓

1.5  PRODUCT OWNER REVIEWS EPIC  
        - Approve / Request regenerate  
        ↓
1.6  EPIC READY  
        ↓

1.7  AI STORY GENERATOR (Draft User Stories)  
        - Draft User Stories  
        - Draft Acceptance Criteria  
        - BTC mapping  
        ↓

1.8  AI TECHNICAL REFINEMENT (Draft)
        - Technical Notes (draft)
        - Architecture direction (draft)
        - Technical Test Cases (TTC draft)
        - Edge cases (draft)
        - API/module design (draft)
        ↓

1.9  TECH LEAD — REVIEW & APPROVAL
        - Approve  
        - Fix minor issues  
        - Or request AI regenerate technical refinement  
        ↓

1.10 AI SUBTASK BREAKDOWN (Draft)
        - FE / BE / API / DB tasks  
        - Test-related subtasks  
        - Logging / Permission / Error handling  
        ↓

1.11 DEV TEAM REVIEW SUBTASKS  
        - Validate correctness  
        - Confirm feasibility  
        - Remove blockers  
        ↓

1.12 READY FOR DEV


=====================================================================================================================
PHASE 2 — AI MULTI-AGENT DEVELOPMENT PIPELINE
=====================================================================================================================

    HUMAN              | TL AI (Cursor) (Optional)       | DEV AI (ChatGPT)        | REVIEW AI | DOC AI
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

2.0 Read docs          | Mandatory                        | Mandatory                | Mandatory  | Mandatory
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

                       |
2.1 STAGE 1 — TL AI (Optional)
                       |
                       | NOTE: OPTIONAL  
                       | Reason: Refinement & subtask breakdown already  
                       |          completed in Pre-Dev phase (1.8–1.11).  
                       |
                       | Optional:  
                       | - Add architecture tweaks  
                       | - Expand skeleton if needed  
                       | - Update plan structure  
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

                       |
2.2 STAGE 2 — DEV AI  
                       |
                       | 2.2.1 Pick ONE atomic task  
                       | 2.2.2 Implement code  
                       | 2.2.3 Write tests  
                       | 2.2.4 Run QUALITY GATES  
                       |        composer lint  
                       |        composer test:coverage-check  
                       |        npm run typecheck  
                       |        npm run build  
                       | 2.2.5 Stage ONLY files for that task  
                       | 2.2.6 Ask Human: "Ready to commit?"  
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

2.3 HUMAN APPROVAL REQUIRED         | ← AI waits  
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

                       |
2.4 After approval → DEV AI executes commit  
                       |  
                       | - Commit atomic change  
                       | - Update plan file  
                       | - Add metadata  
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

                       |
2.5 STAGE 3 — REVIEW AI  
                       |
                       | - Review code quality  
                       | - Enforce architecture & standards  
                       | - Approve or request changes  
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

                       |
2.6 STAGE 4 — DOC AI  
                       |
                       | - Auto-update documentation  
                       | - Sync plan ↔ code  
                       | - Generate changelog  
───────────────────────┼──────────────────────────────────┼──────────────────────────┼────────────┼────────────

2.7 FINAL HUMAN APPROVAL  
        - Validate feature completeness  
        - Approve release  
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

2.8 RELEASE — FEATURE COMPLETE