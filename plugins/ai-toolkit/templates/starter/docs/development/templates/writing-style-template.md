---
# === Metadata ===
template_type: "documentation"
created: "2025-10-30"
last_updated: "2025-10-30"
target_audience: ["AI Assistants", "Content Team", "Development Team"]
description: "Writing style and content guidelines for UI copy, documentation, and user-facing text."
title: "Writing Style Guide"
category: "Content"
tags: ["content", "copywriting", "microcopy", "voice-tone"]
---

# Writing Style Guide

**Optional template** for projects with content and copywriting needs. Defines voice, tone, and writing conventions for user-facing text.

## Voice & Tone

### Brand Voice

[Define your brand's writing personality - typically 3-5 characteristics]

Example:
- **Clear & Concise**: Simple language, avoid jargon
- **Friendly but Professional**: Conversational without being casual
- **Helpful**: Guide users, explain actions clearly
- **Positive**: Frame messages constructively
- **Confident**: Authoritative without being arrogant

### Tone by Context

Adjust tone based on user context:

```yaml
Onboarding:
  tone: "Welcoming, encouraging, patient"
  example: "Welcome! Let's get you set up in just a few steps."

Error Messages:
  tone: "Apologetic, helpful, solution-focused"
  example: "We couldn't save your changes. Please check your internet connection and try again."

Success Messages:
  tone: "Celebratory, affirming"
  example: "Perfect! Your project is live."

Empty States:
  tone: "Motivating, guiding"
  example: "No projects yet. Create your first project to get started."

Settings/Technical:
  tone: "Direct, informative, precise"
  example: "Enable notifications to stay updated on project activity."
```

## Microcopy Guidelines

### Buttons & CTAs

```yaml
Principles:
  - Use verb-first labels ("Save Changes" not "Click to Save")
  - Be specific ("Delete Account" not "Confirm")
  - Keep under 3 words when possible
  - Action-oriented language

Examples:
  Primary Actions: "Create Project", "Save Changes", "Send Message"
  Secondary Actions: "Cancel", "Go Back", "Skip"
  Destructive Actions: "Delete Account", "Remove File", "Clear All"

Avoid:
  - Generic labels: "OK", "Submit", "Click Here"
  - Passive voice: "Changes will be saved"
  - Redundancy: "Click to save" (button context implies clicking)
```

### Form Labels & Help Text

```yaml
Labels:
  - Clear and descriptive
  - Sentence case
  - No punctuation (unless question)
  - Examples: "Email address", "Project name", "Payment method"

Help Text:
  - Explain format or requirements
  - Appear below input field
  - Gray text, smaller font
  - Examples:
    - "We'll never share your email"
    - "Choose a unique name for your project"
    - "Must be at least 8 characters"

Placeholder Text:
  - Show format examples only
  - Don't repeat label text
  - Examples:
    - Email: "name@company.com"
    - Phone: "(555) 123-4567"
    - URL: "https://example.com"

Avoid placeholders as labels:
  - Bad: Placeholder "Enter your email" with no label
  - Good: Label "Email address" with placeholder "name@company.com"
```

### Error Messages

```yaml
Structure:
  1. What went wrong (specific)
  2. Why it happened (if helpful)
  3. How to fix it (actionable)

Examples:
  Good: "Email address is required. Please enter your email to continue."
  Bad: "Error: Field validation failed"

  Good: "Password must be at least 8 characters. Add 3 more characters."
  Bad: "Invalid password"

  Good: "This username is taken. Try adding numbers or your name."
  Bad: "Username exists"

Tone:
  - Never blame the user
  - Avoid technical jargon
  - Be constructive, not just descriptive
  - Use "Please" for requests
```

### Success Messages

```yaml
Principles:
  - Confirm what was accomplished
  - Be specific about the action
  - Positive but not overly enthusiastic

Examples:
  - "Project created successfully"
  - "Changes saved"
  - "Password updated"
  - "Invitation sent to team@company.com"

Avoid:
  - Vague: "Success!"
  - Overly enthusiastic: "Woohoo! You did it! Amazing!"
  - Technical: "Database record updated"
```

### Empty States

```yaml
Structure:
  1. Explain why it's empty
  2. Suggest next action
  3. Optionally provide context/benefit

Examples:
  Good:
    "No projects yet. Create your first project to start collaborating with your team."

  Bad:
    "No data available"

Components:
  - Heading: State what's empty
  - Description: Brief explanation
  - CTA: Clear action button
  - Optional: Illustration or icon
```

### Loading & Progress

```yaml
Short waits (< 2 seconds):
  - Show spinner, no text
  - Or: "Loading..."

Medium waits (2-10 seconds):
  - Specific text: "Uploading files...", "Processing payment..."
  - Progress indicator if possible

Long operations (> 10 seconds):
  - Specific stages: "Analyzing data... (Step 2 of 3)"
  - Estimated time when possible: "About 30 seconds remaining"
  - Keep user informed of progress
```

## Capitalization & Formatting

### Capitalization Rules

```yaml
Headings:
  style: "Sentence case"
  examples:
    - "Create new project"
    - "Account settings"
    - "Billing and payments"
  avoid:
    - "Create New Project" (title case)
    - "ACCOUNT SETTINGS" (all caps)

Buttons:
  style: "Sentence case for multi-word, title case for single"
  examples:
    - "Save changes" (multi-word, sentence case)
    - "Cancel" (single word)
    - "Delete" (single word)

Navigation:
  style: "Sentence case"
  examples:
    - "Dashboard"
    - "Team settings"
    - "Billing history"

Labels:
  style: "Sentence case"
  examples:
    - "Email address"
    - "Project name"
    - "Payment method"

Acronyms:
  style: "All caps"
  examples: "API", "URL", "JSON", "PDF", "HTML"
```

### Numbers & Lists

```yaml
Numbers:
  - Use numerals: "3 items" not "three items"
  - Spell out: "one-time setup" (qualitative)
  - Large numbers: "1.5 million" or "1,500,000"

Lists:
  - Parallel structure (all start with verbs or all nouns)
  - Complete sentences end with period
  - Fragments don't end with period
  - Capitalize first word consistently

Dates & Times:
  - Format: "Jan 15, 2025" or "January 15, 2025"
  - Time: "3:00 PM EST" or "15:00"
  - Relative: "2 hours ago", "Just now", "Yesterday"
```

### Punctuation

```yaml
Periods:
  - Use in complete sentences
  - Skip in headings, labels, buttons
  - Skip in single-sentence help text under inputs

Exclamation Points:
  - Use sparingly
  - Only for genuine excitement or celebration
  - Never in error messages

Question Marks:
  - Use in questions to users
  - Example: "Delete this project?"

Commas:
  - Oxford comma: Yes
  - Example: "projects, files, and settings"
```

## Grammar & Usage

### Contractions

```yaml
Use: Yes (makes tone friendlier)

Examples:
  - "We'll send you a confirmation"
  - "You're all set"
  - "Couldn't save changes"

Avoid in:
  - Legal or financial contexts
  - Error messages (can sound flippant)
```

### Active vs. Passive Voice

```yaml
Prefer Active:
  - Active: "Save your changes before leaving"
  - Passive: "Changes should be saved before leaving"

Use Passive When:
  - Focusing on action, not actor
  - Example: "Your file was uploaded" (focus on file)
```

### Person & Point of View

```yaml
Use "You" for Users:
  - "Your projects"
  - "You have 3 notifications"

Use "We" for the Product/Company:
  - "We'll send you an email"
  - "We couldn't process your request"

Avoid Third Person:
  - Don't: "The user must enter their email"
  - Do: "Enter your email"
```

## Content Types

### Tooltips

```yaml
Purpose: Provide brief, contextual help

Guidelines:
  - Keep to 1-2 short sentences
  - Explain what, not why
  - Don't repeat visible text
  - Triggered on hover, not click

Examples:
  - Icon tooltip: "Export to CSV"
  - Feature tooltip: "Enable this to receive email notifications"
```

### Confirmation Dialogs

```yaml
Structure:
  1. Clear question
  2. Explain consequences (if destructive)
  3. Action buttons (confirm + cancel)

Examples:
  Destructive action:
    Title: "Delete project?"
    Body: "This will permanently delete 'Project Name' and all its files. This cannot be undone."
    Buttons: "Delete" (destructive) + "Cancel"

  Non-destructive:
    Title: "Leave without saving?"
    Body: "Your changes will be lost."
    Buttons: "Leave" + "Stay"
```

### Notifications

```yaml
Toast Notifications:
  - Brief: 1 sentence maximum
  - Auto-dismiss after 3-5 seconds
  - Examples:
    - "File uploaded"
    - "Link copied to clipboard"
    - "Settings saved"

Alert Banners:
  - More detail than toasts
  - User-dismissible
  - Persist until acknowledged
  - Examples:
    - "Your trial ends in 3 days. Upgrade to continue using premium features."
```

## Accessibility

### Screen Reader Considerations

```yaml
Alt Text:
  - Describe image content and function
  - Skip decorative images (alt="")
  - Keep concise but informative

ARIA Labels:
  - Provide context for icon-only buttons
  - Example: <button aria-label="Close dialog">×</button>
  - Announce dynamic content changes

Landmarks:
  - Use semantic HTML (nav, main, aside)
  - Label multiple instances: <nav aria-label="Main navigation">
```

### Plain Language

```yaml
Principles:
  - Use common words
  - Short sentences (< 20 words average)
  - Active voice
  - Avoid jargon and technical terms
  - Define acronyms on first use

Reading Level:
  - Target: 8th grade reading level
  - Tool: Hemingway Editor, Grammarly readability
```

## AI Assistant Instructions

When writing UI copy:
1. Match the established voice and tone
2. Follow capitalization and formatting rules
3. Keep microcopy concise and actionable
4. Use "you" for users, "we" for the product
5. Prioritize clarity over cleverness
6. Test error messages for helpfulness
7. Ensure accessibility with proper labels

For strategic content decisions (voice definition, messaging architecture):
```bash
/adr "content strategy decision"
```

## Related Documentation

- Design overview: `docs/project/design-overview.md` (approved designs and patterns)
- UI design guidelines: `docs/development/conventions/ui-design-guidelines.md` (design tokens and standards)
- API guidelines: `docs/development/conventions/api-guidelines.md` (technical writing)
- ADRs: `docs/project/adrs/` (architectural decisions)

## Maintenance

Update this guide when:
- Brand voice evolves
- New content patterns emerge
- User feedback indicates confusion
- Company messaging changes

Review quarterly to ensure consistency across product.
