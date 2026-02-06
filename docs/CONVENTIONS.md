# Coding Conventions

## General

- **Commits**: Use conventional commits (feat:, fix:, docs:, refactor:, test:, chore:)
- **Branches**: main (production), develop (development), feature/* (features)
- **Comments**: Explain WHY, not WHAT
- **Error handling**: Always handle errors explicitly

## TypeScript (Bun.js services)
```typescript
// Use concise, descriptive names
const jobId = 'linkedin_123';  // Good
const j = 'linkedin_123';      // Bad

// Async/await over promises
async function fetchJobs() {   // Good
  const jobs = await scrape();
  return jobs;
}

// Explicit types
interface JobPosting {         // Good
  job_id: string;
  title: string;
}

// Structured logging
logger.info('Job scraped', { jobId, source }); // Good
console.log('Job scraped');                     // Bad
```

## Python (NLP service)
```python
# Follow PEP 8
# Use type hints
def extract_skills(description: str) -> List[str]:
    """Extract skills from job description.
    
    Args:
        description: Job description text
        
    Returns:
        List of extracted skill names
    """
    pass

# Use descriptive names
skill_extractor = SkillExtractor()  # Good
se = SkillExtractor()               # Bad
```

## Go (Aggregation, Auth services)
```go
// Follow Go idioms
// Error handling
if err != nil {
    return fmt.Errorf("failed to process: %w", err)
}

// Use meaningful names
jobCount := len(jobs)  // Good
jc := len(jobs)        // Bad

// Comments on exported functions
// CalculateTrends computes skill trends for the given timeframe
func CalculateTrends(timeframe string) ([]Trend, error) {
    // ...
}
```

## Git Workflow
```bash
# Feature development
git checkout -b feature/skill-extraction
git add .
git commit -m "feat(nlp): add skill extraction with spaCy"
git push origin feature/skill-extraction

# Create PR, review, merge to develop
# After testing, merge develop -> main
```