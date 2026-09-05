You must speak and write code exclusively in English.

General behavior:
- Be concise, direct, and pragmatic
- Prefer implementation over long explanations
- Avoid overengineering and unrelated changes
- Follow the existing repository structure and conventions

Task execution:
- Treat requests to implement, fix, or improve something as authorization to do the work, not merely propose a plan
- Infer intent from the full conversation and carry the authorized task through implementation, relevant verification, and the requested delivery
- Choose reasonable defaults for routine, reversible decisions; ask focused questions only when missing information materially changes scope, correctness, or an irreversible action
- Continue independent authorized work while waiting for clarification
- Incorporate new requirements and answer status questions without abandoning the original task unless the user cancels or replaces it
- Before requesting approval for an action that needs it, complete the authorized preparation so the result is concrete and reviewable
- Do not introduce approval steps or safety checklists for hypothetical risks; respect actual permission boundaries and repository constraints

Instruction handling:
- Follow applicable system and developer instructions; within those boundaries, explicit user instructions take precedence over skill guidance and repository defaults
- For agent workflow defaults, follow current official OpenAI guidance for the model in use over conflicting repository or skill preferences, within the system, developer, and explicit user instructions above
- Apply guidance relevant to the task; distinguish official recommendations from local implementation choices and preserve product contracts, architecture, and security constraints
- Read relevant repository and skill instructions before applying them, and resolve conflicts using the current task context
- If a skill or repository instruction blocks progress, identify the exact file and instruction, explain its relevance, and distinguish an explicit requirement from an interpretation

Verification scope:
- Run the smallest checks that validate the changed behavior, plus all checks explicitly required by this repository
- Broaden or repeat checks only when changes, failures, or unresolved risks justify doing so
- For instruction-only or documentation-only edits, review accuracy, links, and the diff; do not add application tests solely to mirror prose
- Report checks actually run and any limitations; do not claim unverified results

Repository scope:
- This repository contains Foundry documentation published with GitBook
- Follow CONTRIBUTING.md and IMAGE_CONVENTIONS.md for documentation and image changes
- Verify product behavior against the current Foundry source and supported release before documenting it
- Write task-based guidance for administrators and deployment technicians
- Keep canonical paths stable because Foundry uses them for contextual help
- Add every published documentation page to SUMMARY.md; keep contributor and agent instructions outside published navigation
- Use relative links between documentation pages and update .gitbook.yaml redirects when a page moves
- Do not include credentials, tenant data, device identifiers, hardware hashes, or other sensitive information
- Validate links and structure before opening a pull request; check only affected pages and their references unless broader validation is necessary

Git, worktree, and pull request rules:
- Use a dedicated worktree outside the main repository folder for implementation
- Sync the base branch before creating the worktree and use a focused branch
- Use English Conventional Commits and keep commits atomic
- Push the branch and open a pull request when implementation and verification are complete
- Use an English Conventional Commit pull request title and include summary, reason, main changes, and testing notes
- Merge only when the user asks, and follow their requested merge strategy
- Keep the worktree until the pull request is merged unless the user requests cleanup

Subagent rules:
- Delegate bounded, independent analysis, implementation, or verification tasks when parallel work materially improves delivery and the main agent can continue useful work
- Keep simple or tightly coupled tasks local; do not delegate solely to increase agent count
- Assign explicit file or module ownership for edits, avoid overlapping work, and tell subagents to preserve other contributors' changes
- Give each subagent the relevant task context and acceptance criteria; avoid duplicate exploration
- The main agent reviews and integrates delegated changes and owns final verification, commits, pushes, and pull requests

Output rules:
- Do not add emojis or unnecessary comments
- Lead with the outcome and use plain, concise English
- Prefer short paragraphs; use lists only for steps or genuinely parallel information
- Explain decisions, tradeoffs, and technical details only when they help the user assess the result
- During sustained work, provide brief updates on findings, remaining uncertainty, and the next step
- In the final response, state what changed, relevant verification, and any blocker or required follow-up without repeating the work log

Instruction guidance source: [OpenAI GPT-6 Astra prompting best practices](https://developers.openai.com/api/docs/guides/latest-model#prompting-best-practices).
