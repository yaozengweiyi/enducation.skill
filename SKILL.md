---
name: education
description: |
  Design educational courses that teach complex software engineering topics through 
  progressive case studies, interactive web interfaces, and multi-modal learning. 
  Use this skill when building teaching platforms, documentation systems, or 
  interactive tutorials that need to break down complex systems for beginners.
  Keywords: education, course, tutorial, teaching, progressive-disclosure, case-study,
  interactive-learning, documentation
---

# Education Skill

Build education platforms that transform complex software engineering topics into 
beginner-friendly learning experiences. This skill captures the pattern of 
**progressive case studies + interactive web interfaces** used to teach 
multi-file, multi-mechanism projects.

---

## Core Philosophy

> **Learning happens when complexity is revealed, not when it's dumped.**

A well-designed educational system operates like a camera lens: it starts wide 
(simple overview), zooms in (specific mechanism), then pans out (how it fits 
the whole). The goal is never to show everything at once, but to guide the 
learner through a sequence of "aha moments" -- each new piece answering a 
question the previous piece naturally raised.

The three pillars:
1. **Mental model first** -- learners must understand *why* before *how*
2. **Progressive disclosure** -- reveal complexity one layer at a time
3. **Multi-modal reinforcement** -- text, visuals, simulation, and code all 
   teach the same concept from different angles

---

## The Educational Architecture

An educational course built with this pattern has four layers:

```
+-----------------------------------------------------------+
| Layer 4: WEB PLATFORM         Interactive UI             |
| (Tabs: Learn | Simulate | Code | Deep Dive)              |
+-----------------------------------------------------------+
| Layer 3: DOCUMENTATION        Multi-language markdown    |
| (Mental model → Code snippet → Diff table → Try it)      |
+-----------------------------------------------------------+
| Layer 2: REFERENCE CODE        Runnable implementations  |
| (Self-contained files, incremental versions)             |
+-----------------------------------------------------------+
| Layer 1: CONTENT EXTRACTION    Build-time generation      |
| (Auto-parse source → JSON → power the web)               |
+-----------------------------------------------------------+
```

### Layer 1: Reference Code (The Ground Truth)

The executable source files are the foundation. They constrain and define 
what the education platform can teach. Key principles:

**Self-contained files**: Each session is a standalone, runnable file. No 
imports from sibling sessions. A learner can `python s03.py` and it works 
independently, even though it conceptually builds on s02.

**Incremental versions**: Each file adds exactly one mechanism. The core 
invariant (e.g., the agent loop) never changes -- only the harness around it 
grows. This teaches that complexity is additive, not mutative.

**Naming convention**: Use a consistent prefix-based naming scheme to encode 
progression order and phase grouping:

```
s01_agent_loop.py      # Phase 1: The Loop
s02_tool_use.py        # Phase 1: The Loop
s03_todo_write.py      # Phase 2: Planning & Knowledge
s04_subagent.py        # Phase 2: Planning & Knowledge
s05_skill_loading.py   # Phase 2: Planning & Knowledge
s06_context_compact.py # Phase 2: Planning & Knowledge
s07_task_system.py     # Phase 3: Persistence
s08_background_tasks.py # Phase 3: Persistence
s09_agent_teams.py     # Phase 4: Teams
...
s_full.py              # Capstone: all mechanisms combined
```

**Non-negotiable constraints**:
- Files MUST be independently runnable
- The progression order MUST be strictly defined 
- Each version MUST have a single "core addition" -- the one new thing it teaches
- The core invariant across versions MUST remain untouched

### Layer 2: Documentation (The Mental Model)

Each session gets a documentation page with a **strict, repeatable structure** 
that teaches the concept before showing the code.

#### Documentation Template

```markdown
# Version ID: Short Descriptive Title

`[Prev] > [ CURRENT ] Next > Next > ...`

> *"Motto"* -- a memorable one-liner capturing the session's insight.
> 
> **Harness layer**: Category this mechanism belongs to.

## Problem

One paragraph describing the concrete problem this session solves.
Reference the previous session's limitation to create a natural "why":
- What couldn't the agent do before?
- What friction did the user experience?
- Why is a new mechanism needed?

## Solution

ASCII DIAGRAM showing the architecture (MANDATORY -- must come before any code).

```
+--------+      +-------+      +------------------+
|  Input  | ---> | Core  | ---> |  New Mechanism   |
|         |      |       |      |  +-----+-----+  |
+--------+      +---+---+      +------------------+
                     ^                |
                     |   feedback     |
                     +----------------+
```

The diagram is the most important visual element. Keep it:
- ASCII art (no external images needed)
- Clearly labeled boxes with straight-line connections
- Under 15 lines
- Showing data flow (not just component boxes)

Then a 1-2 sentence plain-English explanation of the diagram.

## How It Works

Step-by-step walkthrough with MINIMAL code snippets. Each step:
1. A plain English explanation (1-2 sentences)
2. A 3-8 line code snippet showing only the relevant lines

```python
# Show only the mechanism, not boilerplate
handler = TOOL_HANDLERS.get(block.name)
output = handler(**block.input) if handler else f"Unknown"
```

Never show the full file. Always show just enough to illustrate the concept.
The full source is available in the Code tab -- keep the docs focused on
understanding.

## What Changed

A BEFORE/AFTER table making the delta explicit:

| Component      | Before (s02)       | After (s03)           |
|----------------|--------------------|-----------------------|
| Tool count     | 4                  | 5 (+todo_write)       |
| New class      | (none)             | `TodoManager`         |
| Agent loop     | Unchanged          | Unchanged             |
| LOC            | 154                | 276                   |

This table is critical: it teaches learners to think in terms of deltas,
not absolutes. Always include an "Unchanged" row to show the invariant.

## Try It

Runnable commands with example prompts (NOT just `python file.py`):

```sh
python agents/s03_todo_write.py
```

1. `Build a CLI tool that converts JSON to CSV`
2. `Add error handling and tests to the converter`
3. `Create documentation for the tool`

These prompts should be specifically chosen to exercise the new mechanism.
```

#### Documentation Principles

1. **Mental model first** -- the ASCII diagram always comes before code
2. **Problem → Solution ordering** -- learners must feel the pain before seeing the cure
3. **Minimal code snippets** -- 3-8 lines each, no full file dumps
4. **Delta-oriented** -- always compare to the previous version
5. **Multi-language** -- maintain identical structure across all locales (en/zh/ja/etc.)
6. **The invariant is sacred** -- always highlight what DIDN'T change

### Layer 3: Content Extraction Pipeline

A build-time script that parses reference code and documentation into 
structured JSON, powering the entire web experience. This is the glue 
layer between the source files and the frontend.

#### Extraction Responsibilities

```typescript
// Pseudocode for the extraction pipeline
function extractEverything() {
  // 1. Parse agent source files
  for each Python file in agents/:
    extract:
      - Classes (name, start/end line numbers)
      - Functions (name, signature, start line)
      - Tools (names from dict literals)
      - LOC (non-blank, non-comment lines)
      - Full source text
  
  // 2. Build version index
  sort versions by predefined order
  compute newTools per version (set difference from previous)
  
  // 3. Compute diffs between adjacent versions
  for each adjacent pair in learning path:
    compute:
      - New classes (names not in previous)
      - New functions (names not in previous)
      - New tools (names not in previous)
      - LOC delta
  
  // 4. Parse documentation
  for each locale directory (en/, zh/, ja/):
    for each markdown file:
      extract:
        - Version ID (from filename convention)
        - Title (from first # heading)
        - Full markdown content
  
  // 5. Write JSON output to web/src/data/generated/
  write versions.json  // VersionIndex: versions[] + diffs[]
  write docs.json      // DocContent[]: version, locale, title, content
}
```

Key design decisions:
- **Generated data is committed** -- the JSON files live in `data/generated/` 
  and are committed to git. The web build reads them directly. Extraction only 
  re-runs when source files change.
- **Fallback on missing source** -- Vercel builds without the Python repo; 
  the pre-committed generated data handles this gracefully.
- **Filename convention as truth** -- version IDs are parsed from filenames 
  (`s01_agent_loop.py` → `s01`). No separate manifest needed.

#### Type Definitions

The data types that bridge extraction and frontend:

```typescript
// Core version metadata
interface AgentVersion {
  id: string;              // "s01"
  filename: string;        // "s01_agent_loop.py"
  title: string;           // "The Agent Loop"
  subtitle: string;        // "Bash is All You Need"
  loc: number;             // Lines of code
  tools: string[];         // All tool names
  newTools: string[];      // Tools added in this version
  coreAddition: string;    // "Single-tool agent loop"
  keyInsight: string;      // Memorable one-liner insight
  classes: { name, startLine, endLine }[];
  functions: { name, signature, startLine }[];
  layer: string;           // "tools" | "planning" | "memory" | ...
  source: string;          // Full source code text
}

// Diff between two versions
interface VersionDiff {
  from: string;            // "s01"
  to: string;              // "s02"
  newClasses: string[];
  newFunctions: string[];
  newTools: string[];
  locDelta: number;
}

// Documentation content per locale
interface DocContent {
  version: string;
  locale: "en" | "zh" | "ja";
  title: string;
  content: string;         // Raw markdown
}
```

### Layer 4: Web Platform (Interactive Learning)

The web UI provides four distinct modalities for each session, organized 
as tabs. This is the key innovation: learners can engage with the same 
concept through whichever modality suits their learning style.

#### The Tabbed Interface

Every session page has four tabs:

```
+----------------------------------------------------+
| [ Learn ]  [ Simulate ]  [ Code ]  [ Deep Dive ]   |
+----------------------------------------------------+
|                                                    |
|  (Content changes based on active tab)             |
|                                                    |
+----------------------------------------------------+
```

Each tab loads lazily -- only the active tab's component renders.

##### Tab 1: Learn (Documentation)

Renders the markdown documentation for the current session. A markdown 
renderer component processes the raw markdown content from `docs.json`, 
converting it to HTML with syntax highlighting for code blocks.

**Implementation notes**:
- Use `unified` / `remark` / `rehype` for markdown → HTML conversion
- Preserve the ASCII diagram formatting (monospace, no wrapping)
- Syntax-highlight code blocks
- Support both light and dark theme

##### Tab 2: Simulate (Agent Loop Simulator)

An interactive step-through simulator that replays pre-defined scenarios. 
This shows the learner exactly what happens inside the system at each step 
of execution.

**Scenario JSON structure**:
```json
{
  "version": "s01",
  "title": "The Agent Loop",
  "description": "A minimal agent that uses only bash to accomplish tasks",
  "steps": [
    {
      "type": "user_message",
      "content": "Create a file called hello.py that prints 'Hello, World!'",
      "annotation": "User sends a task to the agent"
    },
    {
      "type": "tool_call",
      "content": "echo 'print(\"Hello, World!\")' > hello.py",
      "toolName": "bash",
      "annotation": "Tool call: the model generates a bash command"
    },
    {
      "type": "tool_result",
      "content": "",
      "toolName": "bash",
      "annotation": "Bash returns empty output (success)"
    },
    {
      "type": "assistant_text",
      "content": "Done! Created hello.py...",
      "annotation": "stop_reason != 'tool_use' -> loop breaks"
    }
  ]
}
```

**Step types** (the message taxonomy):
- `user_message` -- initial request from user (blue)
- `assistant_text` -- model's final response (purple)  
- `tool_call` -- model requests a tool (dark gray)
- `tool_result` -- tool execution result (green)

**Simulator UI components**:
```
+--------------------------------------------------+
|  [▶ Play] [⏸ Pause] [→ Step] [↺ Reset]  Speed ▼ |
+--------------------------------------------------+
|  [user] Create a file called hello.py...          | ← blue
|  [assistant] I'll create that file for you.       | ← gray
|  [bash] echo 'print("Hello")' > hello.py          | ← gray (tool call)
|  [result] (success)                                | ← green
|  [assistant] Done! Created hello.py...             | ← purple
+--------------------------------------------------+
```

**Simulator behavior**:
- `Play`: Auto-advance through steps with configurable speed
- `Pause`: Stop auto-advance
- `Step`: Advance one step manually
- `Reset`: Clear all steps, return to start
- Messages animate in with `framer-motion` spring animations
- Scroll container auto-scrolls to latest message
- Step annotations explain what's happening at each point

**Hooks**:
- `useSimulator`: Manages step state (current index, play/pause, speed)
- `useSteppedVisualization`: Same concept but for SVG flowchart animations

##### Tab 3: Code (Source Viewer)

Shows the full Python source code with syntax highlighting. Features:
- File name displayed as a header
- Line numbers
- Syntax highlighting for Python
- Scrollable viewport
- Responsive layout

The source comes directly from the extracted data -- no runtime file reads.

##### Tab 4: Deep Dive (Architecture)

A composite view showing advanced analysis:

1. **Execution Flow** -- Data flow diagram of how the mechanism works
2. **Architecture Diagram** -- Component/class diagram
3. **What's New** -- Diff table showing new classes, functions, tools, LOC delta
4. **Design Decisions** -- Why certain choices were made

This tab is for learners who have absorbed the basics and want to go deeper.

#### Hero Visualization

Above the tabs, every session page shows a custom interactive visualization 
of the core concept. This is an **animated SVG flowchart** that:

- Shows the architecture as a node-and-edge graph
- Animates through steps with Framer Motion
- Highlights active nodes and edges with glow effects
- Shows a parallel panel of accumulating data (e.g., `messages[]`)
- Supports auto-play and manual step-through

**Visualization component structure**:
```typescript
// Lazy-loading registry
const visualizations: Record<string, LazyComponent> = {
  s01: lazy(() => import("./s01-agent-loop")),
  s02: lazy(() => import("./s02-tool-dispatch")),
  // ...
};

// Route to correct component by version ID
export function SessionVisualization({ version }: { version: string }) {
  const Component = visualizations[version];
  if (!Component) return null;
  return <Component title={t(version)} />;
}
```

Each visualization implements these core concepts:
- **Node definitions**: Array of `{id, label, x, y, w, h, type}` objects
- **Edge definitions**: Array of `{from, to, label?}` objects
- **Active nodes per step**: Which nodes light up at each step
- **Active edges per step**: Which edges animate at each step
- **Step annotations**: `{title, description}` explaining each step

**Color palette** (dark mode aware):
- Active nodes: blue fill with glow filter
- End nodes: purple fill with glow filter
- Inactive nodes: neutral fill
- Active edges: thicker stroke with animated color
- Arrow markers: styled per active state

#### Navigation Structure

The platform needs two primary navigation views:

**Sidebar (Version List)**:
```
Learn
├── Phase 1: The Loop
│   ├── s01 The Agent Loop
│   └── s02 Tools
├── Phase 2: Planning & Knowledge
│   ├── s03 TodoWrite
│   ├── s04 Subagents
│   ├── s05 Skills
│   └── s06 Context Compact
├── Phase 3: Persistence
│   ├── s07 Task System
│   └── s08 Background Tasks
├── Phase 4: Teams
│   ├── s09 Agent Teams
│   ├── s10 Team Protocols
│   ├── s11 Autonomous Agents
│   └── s12 Worktree Isolation
```

**Alternative views**:
- **Timeline view**: All sessions on a chronological vertical timeline
- **Layers view**: Sessions grouped by architectural layer (tools, planning, 
  memory, concurrency, collaboration)
- **Diff view**: Side-by-side comparison between two versions

#### Internationalization (i18n)

All user-facing text strings live in JSON files organized by locale:

```
src/i18n/messages/
  en.json
  zh.json
  ja.json
```

Each file contains key-value pairs for all UI strings. At runtime, a 
`useTranslations` hook provides locale-aware string lookups. Documentation 
content is served per-locale from `docs/{locale}/`.

#### Route Structure

```
/                         → Redirect to /en/
/en/                      → Home page
/en/timeline              → Timeline view
/en/layers                → Architecture layers view
/en/compare               → Version comparison
/en/s01                   → Session detail (4 tabs)
/en/s01/diff              → Code diff between s01 and previous
```

Pattern: `/[locale]/(learn)/[version]` maps to the version detail page.

---

## The Session Formula (Summary)

Every session must answer these questions in this exact order:

| # | Section | Purpose | Required |
|---|---------|---------|----------|
| 1 | **Motto** | Memorable one-liner | Yes |
| 2 | **ASCII Diagram** | Visual architecture before code | Yes |
| 3 | **Problem** | Why this mechanism is needed | Yes |
| 4 | **Solution** | How the mechanism works (plain English) | Yes |
| 5 | **How It Works** | Step-by-step with minimal code snippets | Yes |
| 6 | **What Changed** | Before/After comparison table | Yes |
| 7 | **Try It** | Runnable prompts (not just `python file.py`) | Yes |

---

## Phase Organization

Group sessions into 3-5 phases with clearly escalating complexity. Each 
phase should have a unifying theme:

```
Phase 1: FOUNDATION (sessions 1-2)
  - Absolute minimum: one core loop, one capability
  - Expand capabilities without changing the core

Phase 2: STRUCTURE (sessions 3-6)  
  - Add planning and task tracking
  - Add knowledge loading
  - Add context management

Phase 3: SCALE (sessions 7-8)
  - Add persistence
  - Add background/concurrent execution

Phase 4: COLLABORATION (sessions 9-12)
  - Add multi-agent coordination
  - Add protocols and autonomy
  - Add isolation and parallel execution
```

The phase boundaries are visual in the sidebar, the timeline, and the 
layers view. They give learners a sense of progress and context.

---

## Visual Design Principles

### SVG Flowchart Specification

For the hero visualization:
- ViewBox: 500×440 is a good default
- Node types: `rect` (process) and `diamond` (decision)
- Font: monospace, 10-12px
- Colors: use a dark-mode-aware palette hook (`useSvgPalette`)
- Glow: SVG `<filter>` with `feDropShadow`
- Arrows: SVG `<marker>` for arrowheads
- Animation: Framer Motion `<motion.rect>`, `<motion.path>`, `<motion.text>`

### Step Controls (Shared Component)

Every visualization and simulator uses the same control bar:

```
[◀ Prev] [▶ Next] [↺ Reset] [▶ Auto-Play]   Step 3/7
"Execute & Append" — Run the tool, append result to messages[]
```

The step description changes with each step, providing a running commentary.

---

## Content Authoring Workflow

### Adding a New Session

1. **Write the reference code** (`agents/sXX_new_feature.py`)
   - Self-contained, runnable
   - Adds exactly one mechanism
   - The core invariant is unchanged

2. **Write the documentation** (`docs/{locale}/sXX-new-feature.md`)
   - Follow the template exactly (Motto → Diagram → Problem → ...)
   - ASCII diagram first
   - Minimal code snippets
   - Before/After table

3. **Define metadata** (in `constants.ts`)
   ```typescript
   sXX: {
     title: "Feature Name",
     subtitle: "Short Tagline",
     coreAddition: "What was added",
     keyInsight: "Memorable one-liner",
     layer: "tools|planning|memory|concurrency|collaboration",
     prevVersion: "sXX-1"
   }
   ```

4. **Create scenario JSON** (`data/scenarios/sXX.json`)
   - 8-15 steps covering a realistic workflow
   - Mix of user messages, tool calls, tool results, assistant responses
   - Annotate each step with what the learner should notice

5. **Create visualization** (`components/visualizations/sXX-*.tsx`)
   - SVG flowchart of the mechanism
   - At least 5-8 steps
   - Active nodes/edges per step arrays
   - Step annotations explaining each phase

6. **Add to the registry** 
   - Add to `VERSION_ORDER` in constants
   - Add to `visualizations` lazy-loading map
   - Add to `scenarioModules` lazy-loading map in simulator

7. **Run extraction** to regenerate `data/generated/*.json`

8. **Update i18n strings** for new UI elements

### Design Rule: The 30-Second Rule

Every session's core concept should be understandable from the ASCII diagram 
alone within 30 seconds. The diagram is the entry point. If a learner can't 
grasp the concept from the diagram, the diagram needs more work.

---

## Anti-Patterns

| Pattern | Problem | Solution |
|---------|---------|----------|
| Dumping full source code | Overwhelms with irrelevant details | Show only the new mechanism (3-8 lines per snippet) |
| Code before diagram | Learners have no mental model | Always put the ASCII diagram first |
| Skipping the Problem section | Learners don't know why it matters | Always explain what broke before this fix |
| No What Changed table | Learners can't see the delta | Always include a Before/After table |
| All sessions same complexity | No sense of progression | Group into phases, escalate complexity |
| Single learning modality | Excludes different learning styles | Provide Learn + Simulate + Code + Deep Dive tabs |
| Hardcoded content in UI | Can't add sessions without code changes | Extract from source files into JSON at build time |
| No scenarios for simulator | Simulator tab is empty | Every session MUST have a scenario JSON |

---

## Tech Stack Reference

The reference implementation uses:

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Reference Code | Python 3.11+ | Runnable agent implementations |
| Documentation | Markdown (3 locales) | Mental-model-first explanations |
| Extraction | TypeScript (tsx script) | Parse source → JSON |
| Frontend | Next.js 16 (static export) | Web platform |
| Styling | Tailwind CSS 4 | Utility-first responsive design |
| Animations | Framer Motion | SVG node highlighting, message transitions |
| Markdown | unified/remark/rehype | Documentation rendering |
| Diff View | diff library | Between-version code comparison |
| SQL Icons | lucide-react | Icon set |
| i18n | Custom provider + JSON files | Multi-language support |

This stack is intentionally minimal. The value is in the educational 
architecture, not in the technology choices. Any comparable stack 
(React+Tailwind, Vue+UnoCSS, etc.) would work equally well.

---

## Checklist: Building an Educational Platform

When building a new educational platform with this pattern:

- [ ] **Source files are self-contained**: Each version is independently runnable
- [ ] **Incremental versions**: Each adds exactly one mechanism
- [ ] **Core invariant preserved**: The central concept never changes across versions
- [ ] **Phased organization**: Sessions grouped into 3-5 escalating phases
- [ ] **Motto per session**: Memorable one-liner for each version
- [ ] **ASCII diagram per session**: Architecture shown before any code
- [ ] **Problem → Solution structure**: Pain before cure
- [ ] **Before/After table per session**: Explicit delta from previous version
- [ ] **Multi-language docs**: Same structure, all target locales
- [ ] **Build-time extraction**: Source → JSON pipeline (committed output)
- [ ] **Tabbed learning interface**: Learn + Simulate + Code + Deep Dive
- [ ] **Hero visualization per session**: Animated SVG of the mechanism
- [ ] **Simulator scenarios**: Pre-defined JSON steps per session
- [ ] **Source viewer**: Syntax-highlighted code browser
- [ ] **Diff viewer**: Between-version comparison
- [ ] **Phase navigation**: Sidebar with grouped versions
- [ ] **Alternative views**: Timeline and architecture layers
- [ ] **Dark mode**: Light and dark themes throughout
