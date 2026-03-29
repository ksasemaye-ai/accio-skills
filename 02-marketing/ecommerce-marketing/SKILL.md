---
name: ecommerce-marketing
description: >
  E-commerce marketing orchestrator that routes user requests to 19 specialized marketing skills.
  Use when user mentions marketing strategy, product promotion, social media campaign, increase sales,
  launch product, conversion optimization, Instagram marketing, TikTok marketing, 小红书推广,
  Facebook ads, Google ads, product description, landing page, SEO, content calendar, marketing ROI,
  or any e-commerce marketing goal — even if they don't use the word "marketing" explicitly.
  Also trigger for questions like "how do I get more customers", "nobody is buying my product",
  "I need to grow my store", or "帮我推广产品".
---

# E-commerce Marketing Orchestrator

Route marketing requests to 19 sub-skills across 5 capabilities. Adapt to whatever skills the user has installed — do what you can now, then tell them what else they could install.

## Step 1: Check Environment

**Before anything else**, run the environment check to know what's available:

```bash
# Read these two paths from your system prompt:
#   - Account skills dir: the directory containing account-level installed skills
#     (appears in available_skills table paths as ~/.accio/accounts/{accountId}/skills/{name})
#   - Agent skills dir: the AGENT_SKILL_DIR value from your system prompt
# Pass them as positional arguments:
bash scripts/check_environment.sh <account_skills_dir> [agent_skills_dir]
```

Parse the JSON output into three lists:
- **installed**: skills you can activate immediately
- **missing**: skills you cannot use — collect these for the install suggestion at the end
- **apis**: which APIs are configured

**Keep the missing list in memory throughout the conversation.** You will need it at the end.

## Step 2: Gather Context

Ask the user these questions (skip any already answered):

1. **Product/service**: What are you selling? (URL or description)
2. **Goal**: What's the marketing objective? (launch, grow sales, brand awareness, etc.)
3. **Stage**: New product or existing? Any current marketing?
4. **Market**: Global or China-specific?
5. **Constraints**: Budget, timeline, team size?

## Step 3: Route & Execute with Available Skills

Map user intent to capabilities, then **only activate installed skills**:

| User Intent | Capability | Skills (check installed) |
|-------------|-----------|--------------------------|
| Market research / competitors | **Research** | `company-research`, `competitive-landscape`, `people-research` |
| Campaign strategy / launch plan | **Strategy** | `product-marketing-context` → `launch-strategy`, `marketing-ideas`, `content-strategy` |
| Write copy / product descriptions | **Content** | `copywriting`, `product-description-generator`, `social-content`, `instagram-marketing`, `remotion` |
| Post to social media | **Publishing** | `mcp-tools`, `social-media-publisher`, xiaohongshu-explore |
| Conversion / analytics | **Optimization** | `page-cro`, `ab-test-setup` |

Supporting skills: `marketing-psychology`, `review-summarizer`, `social-network-mapper`, `sales-negotiator`

### Execution rules:

1. **Use only installed skills.** For each step in the workflow, check if the skill is installed. If yes, activate it. If not, skip it and note it as a gap.
2. **Do as much as possible.** Even if only 2 out of 7 workflow skills are installed, run those 2 and deliver partial but real value.
3. **Adapt the workflow.** When a skill is missing, explain what that step would have done, then move to the next installed skill.
4. **Suggest max 2-3 skills per turn.** Explain WHY each is needed.
5. **For detailed workflows**, read `references/workflows.md`. For sub-skill I/O specs, read `references/skill-interfaces.md`.

### Workflow patterns (adapt based on what's installed):

**Product launch** (ideal 6-step):
```
product-marketing-context → company-research → launch-strategy
→ product-description-generator → copywriting → social-content
```

**Quick content creation**:
```
product-marketing-context → copywriting / social-content / instagram-marketing
```

**Conversion diagnosis**:
```
page-cro → ab-test-setup → copywriting (apply quick wins)
```

## Step 4: Deliver Results + Install Suggestions

After completing all available workflow steps, **always end with an install recommendation section**.

### Format:

```
## What I did

[Summary of completed steps and deliverables]

## What else you could do

The following skills would unlock additional capabilities for your goal:

| Skill to Install | What It Adds | Priority |
|-------------------|-------------|----------|
| `copywriting` | Marketing copy for landing pages, ads, emails, CTAs | High |
| `product-description-generator` | SEO-optimized product descriptions for Shopify/Amazon/Etsy | High |
| ... | ... | ... |

To install a skill, ask me: "install the [skill-name] skill"
```

### Priority assignment logic:

- **High**: Directly needed for the user's stated goal and has no workaround
- **Medium**: Would improve quality or add a useful secondary capability
- **Low**: Nice to have, not critical for current goal

### API configuration suggestions (only if relevant):

If installed skills need APIs that aren't configured, also mention:

```
## API Setup Needed

Some installed skills need API keys to work fully:

| Skill | API Needed | How to Get It |
|-------|-----------|---------------|
| `company-research` | Exa API | Set EXA_API_KEY env var (get key at exa.ai/api) |
| ... | ... | ... |
```

## Behavioral Rules

1. **Do first, suggest later** — always execute available skills before talking about missing ones
2. **Never block on missing skills** — partial results are better than no results
3. **Be specific about gaps** — don't just say "install more skills", name each one and explain what it adds for THIS user's goal
4. **Adapt to level** — beginners get starter packs, advanced users get optimization tactics
5. **End with measurement** — every workflow should include an analytics step (or note that it's missing)
6. **Don't read unrelated skills** — only read SKILL.md files for skills you are about to activate

## Bundled Resources

| Resource | When to Read |
|----------|-------------|
| `references/workflows.md` | User needs a multi-step campaign or end-to-end launch plan |
| `references/skill-interfaces.md` | Need to check what inputs a sub-skill requires or what it outputs |
| `scripts/check_environment.sh` | **Always run first** before any other step |
