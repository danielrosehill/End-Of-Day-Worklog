# End-Of-Day Worklog - Technical Specification

## Overview

A voice-driven productivity tool that captures end-of-day work retrospectives and delivers organized morning briefings. The system uses Gemini multimodal AI to process voice memos and generate structured project summaries.

## Core Workflow

```
[Voice Recording] → [Audio Preprocessing] → [Gemini Processing] → [Document Generation] → [Storage] → [Morning Email]
```

## Components

### 1. Audio Capture Interface

**Requirements:**
- Responsive web UI (works on desktop and mobile)
- Simple interface: record button + send button
- Authenticated access (user-specific)
- Bookmarkable URL for quick access

**Implementation Options:**
- N8N workflow with web form trigger
- Vercel app with authentication
- Future consideration: unified "business admin panel" housing multiple utilities

### 2. Audio Preprocessing Pipeline

**Steps (in order):**
1. Convert MP3 to mono Opus codec
2. Compress/downsample for lightweight storage
3. Optional: silence truncation
4. Archive preprocessed audio

**Rationale:**
- Provides cleanest input to Gemini
- Reduces storage requirements
- Preserves original audio for reference if AI misinterprets

### 3. Gemini Multimodal Processing

**Input:**
- Preprocessed audio file
- Fixed system prompt

**System Prompt Template:**
```
This is an end-of-day retrospective by the user Daniel. Your task is to organize it into an organized document with today's date.

Organize information by project category:
1. Personal Projects
2. Business Projects
3. Open Source Projects

Within each project, include:
- Progress (what was accomplished)
- Blockers (what's preventing progress)
- To-Do Tomorrow (next actions)
```

**Output:**
- Structured Markdown document

### 4. Document Storage

**Structure:**
```
Google Drive/
└── End-of-Day Retrospectives/
    └── [Year]/
        └── [Month]/
            └── [Day]/
                ├── retrospective.md (or Google Doc)
                └── audio.opus
```

**Requirements:**
- Time-based folder hierarchy
- Each daily folder contains:
  - Generated retrospective document
  - Original/preprocessed audio file

### 5. Notification System

**End-of-Day (immediately after processing):**
- Simple confirmation message only
- Validates pipeline is working
- Does NOT send the document (goal is to "park" the workday)

**Start-of-Day (scheduled for 9:00 AM next day):**
- Email with subject: "Here's your start-of-day brief"
- Contains:
  - Link to generated retrospective document
  - Link to original voice note audio
  - Brief summary/preview

## Technical Considerations

### Orchestration Layer

**Primary Option: N8N**
- Webhook trigger for audio upload
- Audio preprocessing nodes
- Gemini API integration
- Google Drive integration
- Email scheduling

**Alternative: Vercel + Custom Backend**
- More control over UI/UX
- Better for eventual "admin panel" consolidation
- Requires more custom development

### Audio Processing

- Use ffmpeg for format conversion
- Target: mono Opus at low bitrate
- Consider: silence detection/removal for cleaner input

### Authentication

- Required for web UI
- Consider: OAuth with Google (aligns with Drive storage)

## Future Considerations

### Admin Panel Vision
Bundle multiple AI-powered daily utilities into a unified authenticated workspace:
- End-of-day recorder
- Other voice-driven tools
- Centralized access vs scattered N8N URLs

### Deferred Decisions
- Specific orchestration platform (N8N vs Vercel)
- Authentication provider
- Admin panel architecture
- These can be addressed after V1 is functional

## V1 Minimum Viable Product

1. Web form with record + submit buttons
2. Audio upload to processing pipeline
3. Gemini transcription and structuring
4. Google Doc generation in organized folder
5. Confirmation message on submission
6. Scheduled morning email with document link

## Success Criteria

- End-of-day recording takes < 5 minutes
- Pipeline processes reliably without intervention
- Morning email arrives by 9:00 AM
- Generated documents are well-organized and actionable
- User successfully "disconnects" after recording (no need to stay engaged)
