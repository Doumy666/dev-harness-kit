# DSH supplemental reviewer

## Purpose

Independent advisory reviewer for same-repository PRs only. All fork PRs are deliberately excluded, including maintainer-authored forks; continue using the original fork-pr-review environment approval and dispatch path. The trusted-base prompt_injection_scan.py scans PR metadata and diff; any nonzero scanner exit blocks DSH. This may reject benign examples of attack strings. Original provider selection, injection gate, Review/Security severity aggregation, approvals and commit statuses remain unchanged. No dev-kit plugin is installed and no /dev-kit command is assumed to work in DSH.

## Activation

After separate maintainer review and adoption onto the trusted base/default branch, add DEEPSEEK_API_KEY as an Actions secret and explicitly set repository variable DSH_REVIEW_ENABLED=true. Until then the job is skipped. No variable, secret, environment, ruleset, or default branch is changed by this preparation. PR events must use the base workflow, never a PR-head workflow.

The owner guard restricts this example to Doumy666. Upstream adoption requires an explicit owner/actor policy review. Existing Actions policy may also need maintainer review to allow DSH; this change does not alter that policy. Do not make this advisory job a required replacement for existing checks.

## Pinned contract and permissions

- DSH-action v0.13.0 annotated tag was dereferenced through GitHub API to `497af93119203ca22af4f96f8fe89b3345ad665c`. The release and action.yml were checked. This verifies reference identity, not a full security audit or signature attestation.
- Exact DSH runtime: `0.2.1-rc.2`; controlled mode; Docker isolation; Worker public egress disabled.
- `allow-write: "false"`, `natural-language-write: "false"`, `task-access: read`; custom tools allow only `workspace.read` and `workspace.search`. Shell, edit, subagents and web tools are explicitly denied; no plugin installation is enabled.
- `run-tests: "false"` is intentional: the agent must not execute repository code. DSH treats this as denying mutations, not waiving write validation.
- Token permissions: contents read; pull-requests write is only for DSH review publication (comments/inline findings), not code changes. Progress comments disabled; final review publication can still occur on PR workflows.
- Trusted-base/default checkout only, pinned checkout SHA, credentials not persisted. No PR-head checkout or repository install/build/test command is used by the agent.
- Fixed maintainer prompt: PR bodies, diffs and model output are not interpolated into action capability inputs. DSH labels fetched context as untrusted; this is a documented boundary, not proof against every prompt injection.

## Results and limitations

Inspect the DSH step summary and `result-json`, including operation, conclusion, trust, effective tools, findings and publication errors. Model output is advisory and may be wrong or incomplete. No deployment, production invocation or upstream communication is part of this integration.

No real DeepSeek call or GitHub-hosted DSH workflow run was made during preparation. Static checks cannot verify credentials, quota, Docker/image availability, model behavior, comment publication, Actions allowlists or event-time authorization. PR content is sent to DeepSeek when enabled; review that data-sharing choice before activation. A pinned Action still depends on its runtime, container, npm packages and remote API. Limits (15 minutes, 6 turns, 10 findings) can truncate analysis. Worker-network=false retains the necessary model proxy and does not make the controller offline. Review any future trusted `.dsh` Profile changes before activation.

## Official references

- [Setup and token scopes](https://github.com/Lixiaoyiao/DSH-action/blob/497af93119203ca22af4f96f8fe89b3345ad665c/docs/setup.md)
- [Action input contract](https://github.com/Lixiaoyiao/DSH-action/blob/497af93119203ca22af4f96f8fe89b3345ad665c/action.yml)
- [Security model](https://github.com/Lixiaoyiao/DSH-action/blob/497af93119203ca22af4f96f8fe89b3345ad665c/SECURITY.md)

## Upstream discussion

A narrowly scoped opt-in integration Issue is reasonable after a real maintainer-approved canary run. Present it as an optional alternative or supplement; do not claim proven security or better review quality. No Issue, PR, email or other outreach has been sent.

## Preparation validation (2026-11-11)

Passed: actionlint 1.7.12, official DSH static config checker (offline placeholder credentials only), YAML/input/SHA/permissions and unchanged-review/security/fork-workflow checks. Targeted upstream regression run: 42 passed, 1 failed, exit 1. Failure: test_04_helper_script_exists_and_is_executable expects a POSIX executable bit; Windows stat reports 0o100666, while git index preserves bin/ci-claude-p.sh as 100755. No permission workaround or existing test change was applied. Full Linux suite and real DeepSeek/GitHub-hosted workflow were not run.
