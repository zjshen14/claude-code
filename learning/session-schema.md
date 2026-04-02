# Claude Code Session Data Schema

## Overview

Claude Code sessions are stored as JSONL (JSON Lines) files in the `.claude` directory within the project. Each line is a JSON object representing an entry in the session.

## Core Entry Types (`Entry` Union Type)

The session data consists of different entry types, all defined in `src/types/logs.ts`:

### 1. TranscriptMessage (Main conversation messages)

```typescript
type TranscriptMessage = SerializedMessage & {
  parentUuid: UUID | null
  logicalParentUuid?: UUID | null
  isSidechain: boolean
  gitBranch?: string
  agentId?: string
  teamName?: string
  agentName?: string
  agentColor?: string
  promptId?: string
}
```

Where `SerializedMessage` includes:
- `type`: Message type ('user', 'assistant', 'system', etc.)
- `content`: Message content
- `cwd`: Current working directory
- `userType`: User type
- `sessionId`: Session identifier
- `timestamp`: ISO timestamp
- `version`: Claude Code version
- `gitBranch`: Git branch
- `slug`: Session slug

### 2. Metadata Entries

- `SummaryMessage`: Conversation summaries
- `CustomTitleMessage`: User-set session titles
- `AiTitleMessage`: AI-generated titles
- `TagMessage`: Searchable tags
- `AgentNameMessage`: Agent names
- `AgentColorMessage`: Agent colors
- `AgentSettingMessage`: Agent definitions
- `PRLinkMessage`: GitHub PR links
- `ModeEntry`: Session mode ('coordinator' | 'normal')
- `WorktreeStateEntry`: Worktree session state

### 3. Snapshot Entries

- `FileHistorySnapshotMessage`: File history snapshots
- `AttributionSnapshotMessage`: Character-level contribution tracking
- `ContextCollapseCommitEntry`: Context collapse commits
- `ContextCollapseSnapshotEntry`: Context collapse snapshots

### 4. Other Entries

- `QueueOperationMessage`: Queue operations
- `SpeculationAcceptMessage`: Speculation accepts
- `ContentReplacementEntry`: Content replacement records

## Session Structure

### File Organization

- **Main sessions**: `.claude/<sessionId>.jsonl`
- **Agent sessions**: `.claude/agent-<agentId>/<sessionId>.jsonl`

### Conversation Chain

- Messages form a tree structure using `parentUuid` relationships
- Each message references its parent in the conversation
- The "leaf" messages are the most recent messages in each conversation branch

### Session Metadata

Sessions contain various metadata maps indexed by session ID:
- `summaries`: Conversation summaries
- `customTitles`: User-set titles
- `tags`: Search tags
- `agentNames`: Agent names
- `modes`: Session modes
- `worktreeStates`: Worktree states
- `prNumbers/Urls/Repositories`: PR links

## Loading Process

When loading a session (`loadTranscriptFile`):
1. Parses JSONL entries from the file
2. Builds message map and metadata maps
3. Identifies leaf messages (conversation endpoints)
4. Reconstructs conversation chains
5. Applies context collapse and other transformations

## Key Features

- **Append-only**: New entries are appended to the JSONL file
- **Immutable**: Historical data is preserved
- **Efficient**: Large files use boundary-based loading to skip old data
- **Hierarchical**: Supports main conversations and agent sidechains
- **Metadata-rich**: Extensive tracking of session state, attribution, and context

The schema supports complex features like context collapsing, agent spawning, worktree management, and detailed attribution tracking while maintaining efficient storage and loading.</content>
<parameter name="filePath">/workspaces/claude-code/learning/session-schema.md