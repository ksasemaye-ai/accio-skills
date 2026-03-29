---
name: skillsmp
description: Search SkillsMP marketplace (283K+ agent skills) using REST API with AI semantic search. Use when looking for specialized, research, or niche skills (e.g. arxiv, quantum computing, supply chain). Best for comprehensive searches when skills.sh and ClawHub lack results.
always_apply: true
---

# SkillsMP

Search 283,000+ agent skills using SkillsMP's AI-powered semantic search API.

## When to use

- **Specialized/niche skills**: Research, academic, domain-specific (arxiv, quantum, trading)
- **AI semantic search**: Natural language queries work better than keywords
- **Comprehensive search**: Largest database when other platforms have no results
- **Also check**: `skill-search-strategy` for multi-platform search

**Trade-offs:**
- ✅ Largest database (283K vs ~thousands on other platforms)
- ✅ AI semantic search understands intent
- ⚠️ Slower (5-15s response time)
- ⚠️ Rate limited (500/day)
- ⚠️ Manual installation via GitHub URL

## Quick start

### 1. Get API key

Add to `.env`:

```bash
SKILLSMP_API_KEY="sk_live_skillsmp_xxx..."
```

Get key at: https://skillsmp.com/docs/api

### 2. Search skills

```bash
# Read API key from .env
SKILLSMP_API_KEY=$(grep SKILLSMP_API_KEY .env | cut -d '=' -f2 | tr -d '"')

# AI semantic search (recommended)
curl -X GET "https://skillsmp.com/api/v1/skills/ai-search?q=arxiv+research+papers&limit=5" \
  -H "Authorization: Bearer $SKILLSMP_API_KEY" \
  -H "Accept: application/json"
```

### 3. Install skill from results

Extract the GitHub URL from the response and install using one of these methods. Let the agent decide the appropriate installation directory based on context (personal vs project scope).

```bash
# Method 1: Via skills CLI (preferred, if cross-listed)
npx skills add <author>/<repo>@<skill-name>

# Method 2: Clone repository and copy SKILL.md to the appropriate skills directory
git clone <githubUrl>
cp <repo>/skills/<skill-name>/SKILL.md <target-skills-directory>/

# Method 3: Direct download to the appropriate skills directory
curl -L https://raw.githubusercontent.com/<author>/<repo>/main/skills/<skill>/SKILL.md \
  -o <target-skills-directory>/<skill-name>/SKILL.md
```

## Python usage

```python
from phoenix.config import get_settings

settings = get_settings()
api_key = settings.skillsmp_api_key  # Auto-loaded from .env
```

## API endpoints

### AI Semantic Search (recommended)

**Endpoint:** `GET /skills/ai-search`

**Parameters:**
- `q` (required): Natural language query
- `limit` (optional): Results to return (default 20, max 100)

**Example queries:**
- "arxiv research papers tracking"
- "quantum computing simulation"
- "typescript performance optimization"

**Response format:**

```json
{
  "success": true,
  "data": {
    "data": [
      {
        "score": 0.9956,
        "skill": {
          "name": "arxiv-search",
          "author": "DeevsDeevs",
          "description": "Search arXiv preprints...",
          "githubUrl": "https://github.com/...",
          "skillUrl": "https://skillsmp.com/skills/...",
          "stars": 29,
          "updatedAt": 1770255689
        }
      }
    ]
  }
}
```

### Keyword Search

**Status:** ⚠️ Currently unstable (returns `INTERNAL_ERROR`). Use AI search instead.

## Examples

### Search for arxiv skills

```bash
SKILLSMP_API_KEY=$(grep SKILLSMP_API_KEY .env | cut -d '=' -f2 | tr -d '"')
curl "https://skillsmp.com/api/v1/skills/ai-search?q=arxiv+papers&limit=5" \
  -H "Authorization: Bearer $SKILLSMP_API_KEY" -H "Accept: application/json"
```

**Result:** Found 8 skills including `arxiv-search` (29 stars), `academic-search` (44 stars)

### Search TypeScript skills

```bash
SKILLSMP_API_KEY=$(grep SKILLSMP_API_KEY .env | cut -d '=' -f2 | tr -d '"')
curl "https://skillsmp.com/api/v1/skills/ai-search?q=typescript+best+practices&limit=3" \
  -H "Authorization: Bearer $SKILLSMP_API_KEY" -H "Accept: application/json"
```

### Parse results with jq

```bash
curl ... | jq -r '.data.data[] | "\(.skill.name) by \(.skill.author)\n  \(.skill.description[0:100])...\n  ⭐ \(.skill.stars) | \(.skill.githubUrl)\n"'
```

## Rate limits and errors

**Limits:**
- 500 requests per day per key
- Resets at midnight UTC
- Headers: `X-RateLimit-Daily-Limit`, `X-RateLimit-Daily-Remaining`

**Common errors:**

| Code | Status | Fix |
|------|--------|-----|
| `INVALID_API_KEY` | 401 | Check `.env` for correct key |
| `MISSING_QUERY` | 400 | Add `q` parameter |
| `DAILY_QUOTA_EXCEEDED` | 429 | Wait until midnight UTC |
| `INTERNAL_ERROR` | 500 | Use AI search instead of keyword search |

## Scoring

Interpret AI search match scores:

- **> 0.95**: Excellent match
- **0.90-0.95**: Good match
- **0.85-0.90**: Fair match  
- **< 0.85**: Weak match

## Platform comparison

| Feature | SkillsMP | skills.sh | ClawHub |
|---------|----------|-----------|---------|
| **Size** | 283K+ | ~Thousands | Community |
| **Search** | AI semantic | CLI keyword | CLI keyword |
| **Speed** | Slow (5-15s) | Fast | Fast |
| **Install** | GitHub URL | `npx skills add` | `clawhub install` |
| **Best for** | Research/niche | Common skills | Publishing |

## Troubleshooting

**No SKILLSMP_API_KEY in .env:**
```bash
echo 'SKILLSMP_API_KEY="sk_live_skillsmp_xxx..."' >> .env
```

**API key format:**
- Must start with `sk_live_skillsmp_`
- Get from: https://skillsmp.com/docs/api

**Empty search results:**
- Use descriptive natural language
- Try broader terms
- Combine multiple keywords

## Related skills

- `skill-search-strategy` - Multi-platform search coordinator
- `find-skills` - skills.sh (fast install)
- `clawhub` - ClawHub (version management)
