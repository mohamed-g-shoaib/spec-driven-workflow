# PROJECT-CLARITY-INTAKE.md

Use this checklist when a user request is vague, partial, or missing constraints.

Goal: prevent low-quality output caused by silent assumptions.

---

## Blocking Clarification Gates

Implementation should not begin until all blocking gates are resolved or explicitly defaulted.

1. Outcome

- What should be true when this is done?
- What is the measurable definition of success?

2. Scope

- What is in scope for this session?
- What is explicitly out of scope?

3. Users

- Who is the primary audience?
- What are the top 3 user journeys?

4. Constraints

- Performance targets (if any)
- Accessibility target (minimum WCAG level)
- SEO requirements (if website)
- Compliance/legal/security requirements
- Timeline constraints

5. Content and Data

- What content/data exists today?
- Who owns updates?
- Is there a CMS/API/source of truth?

6. Integrations

- Required external services
- Forbidden external services
- Environment constraints (hosting, auth, analytics, payments)

7. Acceptance Criteria

- Objective pass/fail checks
- Required tests or verification steps

---

## Compact Question Set (Use for Vague Prompts)

Ask at most 7 blocking questions, grouped by theme:

1. Goal: What does success look like for this work?
2. Scope: What should this include right now, and what should it not include?
3. Audience: Who is the main user and what is their primary task?
4. Constraints: Any non-negotiables for accessibility, SEO, performance, legal/security?
5. Content/Data: Where does data come from and who manages it?
6. Integrations: Which services must be used or avoided?
7. Acceptance: What exact checks should pass before we call this done?

---

## Safe Defaults (Only When User Cannot Decide Yet)

Defaults must be labeled as assumptions and logged in session notes.

- Assumption: baseline responsive behavior for desktop + mobile
- Assumption: basic accessibility hygiene (semantic structure, keyboard support)
- Assumption: basic SEO metadata for indexable pages
- Assumption: no third-party integrations unless requested
- Assumption: no user authentication unless requested

Never treat defaults as confirmed requirements.

---

## Assumption Log Format

When assumptions are used, log them explicitly:

- Assumption: [statement]
- Confidence: high | medium | low
- Verification path: [how to verify]
- Impact if wrong: [what changes]

---

## Hallucination Prevention Rules

- Do not invent APIs, constraints, or compatibility claims
- Do not infer product strategy from generic patterns alone
- Verify third-party behavior before coding against it
- If verification is not possible, stop and flag uncertainty

---

## Website-Specific Discovery Addendum

If the request includes a website, also confirm:

1. Website type: marketing, app shell, docs, commerce, portal, or mixed
2. Main routes/pages
3. Primary conversion/action targets
4. Content model and ownership
5. Analytics + consent requirements
6. i18n/RTL requirements
7. Launch definition of done
