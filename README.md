# Skills for Devs Who Want to Understand 
I've been teaching devs to understand what they do, not just imitate, for over a decade.

These skills are designed to help developers, designers, and software engineers to build the best products with AI, while maintaining their knowledge, understanding, experience, and wisdom.

As developers, designers, and managers find their responsibilities merging, they also helps balance the activity of writing good software across the entire SDLC.

At heart, the thesis is that product people should understand and be able to be accountable for the AI-generated things they put into production. This benefits everyone.

## Skills

### do-i-understand

```
npx skills add https://github.com/anthonypalicea/skills --skill do-i-understand
```

This is a reverse code review. The goal is to ensure that you, the developer, understand your AI-generated code well enough to be accountable for it, and it encourages you not to lose your skill in the age of LLM-generation. 

Running `/do-i-understand` looks at the changes about to be pushed (or previous commits if you like), prioritizes changes to review with you, then probes your reasoning, the consequences, the assumptions you're making, and the ways the change could fail, trying to help you find the parts (if any) that you don't yet fully understand or haven't thought through yourself.

Use the report at the end to include in a merge request description for your reviewer or in a personal record for your own review.

#### When to use this skill
You might use this skill to:

1. Maintain your own skill and understanding of your codebase.
2. Make a safe space for junior engineers and require the usage of this skill as part of their PRs. Ask them not to submit the merge request until they are comfortable with their understanding, or to include the report in the PR description with "I don't know" as being a welcome answer that invites pair coding and mentorship.
3. Open source maintainers may require a do-i-understand report in PRs as a light honor system requirement.
