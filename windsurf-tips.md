# Windsurf Tips & Tricks

---

## Initial Setup

### Windows Users: Use WSL (Windows Subsystem for Linux)
- **Why**: Most environments (Kubernetes, playbooks, etc.) are Linux-based
- **How**: Click "Connect to WSL" in the bottom corner of Windsurf
- **Benefit**: Native bash terminal, can run `apt install`, supports all Linux commands
- **Note**: May require Ubuntu installer if it doesn't load automatically

### VPN + WSL Issue (Windows)
- VPN connection can sever WSL's internet connection
- **Fix**: Run in PowerShell: `Get-NetIPInterface -InterfaceAlias "Your interface name" | Set-NetIPInterface -InterfaceMetric 4000`
- Alternative: Use home router instead of VPN when possible

### Docker Without Docker Desktop
- Docker Desktop requires a license - avoid it
- **Solution**: In WSL, run `apt install docker-ce-cli` to get Docker CLI without the desktop
- Podman is also an option but may have issues

---

## Workspace Organization

### Folder Structure
- Attach persistent storage to your Windsurf environment (it needs somewhere to save files)
- Create a `workspaces` folder under your home directory
- Create separate `.code-workspace` files for different projects (e.g., `bubbles-development.code-workspace`, `on-call.code-workspace`)

### Opening Workspaces
- **File > Open Workspace from File** to quickly switch contexts
- Each workspace already has your repos, settings, and context ready to go

---

## Voice Input (Game Changer)

### How It Works
1. Click the microphone/record button in Windsurf
2. Speak naturally (fast speech is fine)
3. It records a WAV file, then uses an LLM to process speech-to-text
4. Result: ~95% accuracy, way better than Siri/Alexa

### Tips
- Recording stops after ~20 seconds
- **Workflow**: Record → copy text → paste → record more → paste again → build up your full prompt
- Voice is faster than typing - you can vibe code for hours without touching the keyboard
- The AI adapts to your speaking style over time

---

## Context Management

### Adding Context to Prompts
- **Drag and drop** files into the chat
- **Copy path**: Right-click file → Copy Path → paste into prompt
- **Terminal output**: Highlight text in terminal → "Send to Chat"
- **@ mentions**: Type `@` to reference files, MCP tools, etc.
- **Open files are context**: Anything open in the editor can be read by the AI

### Context Windows
- Each LLM has different context window sizes:
  - Claude 4.5 models (Sonnet, Haiku, Opus): 200K tokens standard
  - Sonnet 4.5 extended: 1M tokens (beta feature)
- Windsurf auto-grooms context when it gets too large (can remove relevant info)
- **Best practice**: Treat context windows like cattle - kill them often, start fresh

### Starting Fresh
- Click the `+` button to create a new conversation
- Keep different tasks in separate context windows
- Use `Close All` to clear open files and reduce context pollution

---

## LLM Selection

### Available Models
- **Thinking models**: Show reasoning process, good for learning, larger context, more expensive
- **Standard models**: Faster, cheaper, good for routine tasks
- **SWE models** (SW-1, etc.): Black box routing to best backend - you don't know which LLM you get

### Cost/Credits
- Windsurf Pro: ~$15/month for 500 credits (personal plans vary - hmu for a referral code to get us each 250 extra bonus credits)
- Different models cost different amounts (e.g., Opus = higher cost, Sonnet = moderate, Haiku = cheapest)
- Check usage: Your account shows credits used, lines written, messages sent

### Recommendations
- **Sonnet 3.7**: Solid, cost-effective for most work
- **Opus 4.5**: Premium but expensive
- **Free models**: Available for personal use, quality varies
- Ask the AI: "I'm doing X, suggest which LLM to use"

### Switching Models Mid-Conversation
- Click on a previous prompt
- Change the LLM dropdown
- Hit resend - it will rollback changes and reprocess

---

## Chat Mode vs Code Mode

### Chat Mode
- Can read files
- **Cannot** write/modify files
- Good for questions and explanations

### Code Mode  
- Full access to read AND write files
- Has command palette access
- Use this for actual development work

### Pro Tip
When AI gives suggestions instead of doing the work:
> **"No, you need to do it"**

This prompt forces the AI to take action rather than just advise.

---

## Rules, Memories, and Workflows

### Rules
- **Global rules**: Apply everywhere, all the time (hard to manage)
- **Project rules**: Apply only to specific workspaces (recommended)
- Example rule: "Only create new files when asked. Avoid temporary files."
- Rules load at the start of a conversation

### Memories  
- Similar to rules but load with every prompt
- Managed via MCP server (install from marketplace)
- **Prune frequently** to avoid hallucination from stale memories

### Workflows
- Reusable prompts saved as `.md` files in `.windsurf/workflows/`
- Become slash commands (e.g., `/process-incidents`, `/interview-me`)
- **Format**:
```yaml
---
description: Short description of what this does
---
Step-by-step instructions for the AI to follow
```

### Workflow Examples
- `/process-incidents`: Fetches incident page, summarizes active incidents
- `/interview-me`: Daily work journaling, asks about your day, commits to repo
- `/RCA`: Generate RCA documents from Jira, Confluence, chat channels

---

## MCP Servers (Model Context Protocol)

### What They Are
- External tools that extend AI capabilities
- Each server provides "tools" the AI can call
- Your MCP tools are loaded into every context window

### Essential MCP Servers to Install
| Server | Purpose |
|--------|---------|
| **Memory** | Persistent memory across sessions |
| **Fetch** | Fetch any URL from the internet |
| **Sequential Thinking** | Better reasoning for complex tasks |
| **Atlassian** | Access Jira and Confluence |
| **Backstage** | Check who's on call, team info |
| **GitHub** | Repo access (may need config for enterprise GitHub) |

### Installing MCP Servers
1. Open MCP Marketplace in Windsurf
2. Find the server you want
3. Click Install
4. Configure environment variables (API tokens, etc.)

### Notes
- Public marketplace servers may not work with enterprise-specific systems
- Some require Docker/Podman to run containers
- Custom MCP servers can be configured to load almost any type of context you want. Build them if you dont find one using AI agents

### Tagging MCP Tools
Use `@tool-name` to direct prompts to specific MCP servers:
```
Using @fetch, go to this website and summarize it
```

---

## AI README Files

### What They Are
- AI-generated documentation about your codebase
- Not really for humans - optimized for AI consumption

### Why Create Them
- When you return to a project, tell the AI: "Read the AI README"
- Prevents hallucination by giving accurate project context
- Faster than having AI re-analyze the entire codebase

### How to Create
> "I don't have an AI development README in my project. Create one that covers environment structure, code trees, how to develop, etc."

---

## Practical Workflow Examples

### On-Call Incident Management
```
/process-incidents
```
- Fetches incident page
- Summarizes active, restored, resolved incidents
- Tracks what changed since last check
- Generates executive summaries for each incident
- Can send summaries to team members via chat integration

### Daily Work Journaling
```
/interview-me
```
- AI asks about your day
- Records what you accomplished
- Commits to your personal repo
- Reminds you of yesterday's planned tasks

### RCA Generation
Create a workflow that:
1. Searches Jira for the incident ticket
2. Pulls Confluence documentation
3. Reads chat channel history
4. Checks Backstage for relevant people
5. Generates formatted RCA document

---

## Git Integration

### AI as Git Expert
- Don't fear git anymore - ask the AI to do complex operations
- Stashes, feature branches, selective commits, rollbacks
- Example: "These changes are independent thoughts - commit them separately"

### Rollback Changes
- Go back to any previous prompt
- Click resend with modified parameters
- AI will rollback file changes automatically (warns you first)
- Use `git stash` to save work before letting AI delete it

---

## Queuing Prompts

### While AI is Working
- **Enter**: Queue your next prompt (runs after current task)
- **Shift+Enter**: Interrupt and process immediately

### Example
While running `/process-incidents`:
> "After this finishes, send the security incident summary to Gary"

---

## Token Usage Tips

- Token costs vary by model (shown in model selector)
- Thinking models use more tokens but provide reasoning
- Keep context windows clean to avoid waste
- Start fresh conversations for new topics

---

## Beyond Coding: Other AI Uses

### Personal Productivity
- Generate product reviews from bullet points
- Create presentations from scattered notes
- Analyze blood work and medication interactions
- Design physical objects (heat shields, 3D print files)

### Content Generation
- **Gemini Storybook**: Generate illustrated children's books
- **Grok Imagine**: Short video generation (3-10 seconds)
- **Image generation tip**: Have AI describe your photo in detail first, then use that description as the generation prompt

### Financial/Data Analysis
- Feed spreadsheets to AI
- Have it build web-based dashboards
- Forecast pricing models
- Generate visualizations

---

## Key Philosophies

### "No, you need to do it"
Force the AI to take action instead of giving suggestions.

### Treat Context Windows as Cattle
Kill them often. Start fresh. Don't get attached.

### Workflows are Personal
They should match YOUR thinking process, not copied from others.

### Experiment and Iterate
When output isn't right, update your rules/prompts. Hallucinations are learning opportunities.

### You Have a PhD Working for You
Don't limit yourself. If you can describe it, AI can probably help.

---

## Quick Reference

| Task | Solution |
|------|----------|
| Start fresh context | Click `+` button |
| Clear file context | Close All |
| Voice input | Click microphone, speak, paste |
| Force AI action | "No, you need to do it" |
| Add file to context | Drag & drop or `@filename` |
| Run workflow | `/workflow-name` |
| Check credits | Account settings |
| Switch LLM | Click prompt → change model → resend |
| Queue prompt | Type and press Enter while AI working |
| Interrupt AI | Shift+Enter |

---

## Resources

- **Lakera Gandalf**: Practice LLM security/prompt injection (https://gandalf.lakera.ai/)
- **OpenRouter.ai**: LLM usage statistics and model comparison
- **MCP Marketplace**: Built into Windsurf for installing servers
