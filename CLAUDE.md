# Claude Code instructions

You must speak and write code exclusively in English.

## General behavior

- Be concise, direct, and pragmatic
- Prefer implementation over long explanations
- Avoid overengineering and unrelated changes
- Follow the existing repository structure and conventions

## Task execution

- Treat implementation requests as authorization to complete the scoped work, relevant verification, and requested delivery
- Choose reasonable defaults for routine reversible decisions; ask only when missing information materially affects scope, correctness, or an irreversible action
- Continue independent authorized work while awaiting clarification and prepare a reviewable result before requesting necessary approval
- Incorporate follow-up requirements without abandoning the original task unless the user cancels or replaces it
- Respect actual permission boundaries without adding approval steps for hypothetical risks
- Honor task-specific delivery instructions, including requests for no pull request or review before committing and pushing

## Instruction handling

- Follow applicable system and developer instructions; within those boundaries, explicit user instructions take precedence over skill guidance and repository defaults
- For Claude Code workflow defaults, follow current official Anthropic guidance within applicable higher-priority instructions, explicit user requirements, and repository contracts
- Apply guidance relevant to the task; distinguish official recommendations from local implementation choices and preserve product contracts, architecture, and security constraints
- Read applicable nested `CLAUDE.md` files and relevant skill instructions before editing, and resolve conflicts using the current task context
- If a skill or repository instruction blocks progress, identify the exact file and instruction, explain its relevance, and distinguish an explicit requirement from an interpretation

## Skill and documentation tools

- Use only tools, MCP servers, and skills available in the current Claude Code session; do not assume another coding tool's plugins or configuration are installed
- Use the Context7 MCP server, when available, for library or framework APIs, setup, or version-specific behavior; resolve the relevant library and consult documentation matching the repository version before relying on memory
- If Superpowers skills are installed in Claude Code, use the relevant skill for brainstorming, debugging, planning, implementation, review, or verification; read and apply it rather than merely naming it
- Keep skill use proportional to the task and follow the instruction precedence above; do not invoke unrelated skills or add unnecessary workflow steps
- If Context7 or a required skill is unavailable, state the limitation and continue with official documentation or an equivalent workflow where possible; do not claim to have used unavailable tools

## Verification scope

- Run the smallest checks that validate the changed behavior, plus all checks explicitly required by this repository
- Broaden or repeat checks only when changes, failures, or unresolved risks justify doing so
- For instruction-only or documentation-only edits, review accuracy, links, and the diff; do not add application tests solely to mirror prose
- Report checks actually run and any limitations; do not claim unverified results

## Repository scope

- This repository contains Foundry documentation published with GitBook
- Follow CONTRIBUTING.md and IMAGE_CONVENTIONS.md for documentation and image changes
- Verify product behavior against the supported Foundry release and relevant source; label unreleased behavior explicitly rather than presenting main-branch changes as released
- Write task-based guidance for administrators and deployment technicians
- Keep canonical paths stable because Foundry uses them for contextual help
- Add every published documentation page to SUMMARY.md; keep contributor and agent instructions outside published navigation
- Use relative links between documentation pages and update .gitbook.yaml redirects when a page moves
- Do not include credentials, tenant data, device identifiers, hardware hashes, or other sensitive information
- Before opening a pull request, check affected relative Markdown links and HTML image sources, navigation entries in `SUMMARY.md`, and changed redirect targets in `.gitbook.yaml`
- This repository has no checked-in build or link-check script; use focused file and link checks and report their scope rather than assuming a Node build
- Preserve GitBook hint and figure markup; store images in `.gitbook/assets/` following `IMAGE_CONVENTIONS.md`

## Git, worktree, and pull request rules

- Use a dedicated worktree outside the main repository folder for implementation
- Sync the base branch before creating the worktree and use a focused branch
- Use English Conventional Commits and keep commits atomic
- Unless the user specifies another delivery workflow, push the branch and open a pull request when implementation and verification are complete
- Use an English Conventional Commit pull request title and include summary, reason, main changes, and testing notes
- Merge only when the user asks, and follow their requested merge strategy
- Keep the worktree until the pull request is merged unless the user requests cleanup

## Subagent rules

- Use available Claude Code subagents for bounded, independent analysis, implementation, or verification when parallel work materially improves delivery and the main agent can continue useful work
- Keep simple or tightly coupled tasks local; do not delegate solely to increase agent count
- Assign explicit file or module ownership for edits, avoid overlapping work, and tell subagents to preserve other contributors' changes
- Give each subagent the relevant task context and acceptance criteria; avoid duplicate exploration
- The main agent reviews and integrates delegated changes and owns final verification, commits, pushes, and pull requests

## Output rules

- Do not add emojis or unnecessary comments
- Lead with the outcome and use plain, concise English
- Prefer short paragraphs; use lists only for steps or genuinely parallel information
- Explain decisions, tradeoffs, and technical details only when they help the user assess the result
- During sustained work, provide brief updates on findings, remaining uncertainty, and the next step
- In the final response, state what changed, relevant verification, and any blocker or required follow-up without repeating the work log

Claude Code references: [Best practices](https://code.claude.com/docs/en/best-practices) and [project memory](https://code.claude.com/docs/en/memory).
