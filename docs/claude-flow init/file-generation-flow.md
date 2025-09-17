# File Generation Flow

```mermaid
flowchart TD
    START([Init Command]) --> CHECK_ENV{Environment<br/>Detection}

    %% Environment Detection
    CHECK_ENV -->|Git Repo| GIT_MODE[Git Repository Mode]
    CHECK_ENV -->|New Project| NEW_MODE[New Project Mode]
    CHECK_ENV -->|Existing| EXISTING_MODE[Existing Project Mode]

    %% Template Selection Logic
    GIT_MODE --> TEMPLATE_SELECT{Template Selection}
    NEW_MODE --> TEMPLATE_SELECT
    EXISTING_MODE --> TEMPLATE_SELECT

    TEMPLATE_SELECT -->|Claude Code Project| CLAUDE_TEMPLATE[Claude Code Templates]
    TEMPLATE_SELECT -->|MCP Integration| MCP_TEMPLATE[MCP Server Templates]
    TEMPLATE_SELECT -->|Full Stack| FULL_TEMPLATE[Full SPARC Templates]
    TEMPLATE_SELECT -->|Custom| CUSTOM_TEMPLATE[Custom Templates]

    %% Content Generation
    CLAUDE_TEMPLATE --> GEN_CLAUDE[Generate CLAUDE.md]
    MCP_TEMPLATE --> GEN_MCP[Generate MCP Config]
    FULL_TEMPLATE --> GEN_FULL[Generate Full Suite]
    CUSTOM_TEMPLATE --> GEN_CUSTOM[Generate Custom Files]

    %% Directory Creation Sequence
    GEN_CLAUDE --> DIR_CHECK{Directory<br/>Structure Check}
    GEN_MCP --> DIR_CHECK
    GEN_FULL --> DIR_CHECK
    GEN_CUSTOM --> DIR_CHECK

    DIR_CHECK -->|Missing| CREATE_DIRS[Create Directories]
    DIR_CHECK -->|Exists| VALIDATE_DIRS[Validate Structure]

    CREATE_DIRS --> DIR_SRC[Create /src]
    CREATE_DIRS --> DIR_TESTS[Create /tests]
    CREATE_DIRS --> DIR_DOCS[Create /docs]
    CREATE_DIRS --> DIR_CONFIG[Create /config]

    DIR_SRC --> VALIDATE_DIRS
    DIR_TESTS --> VALIDATE_DIRS
    DIR_DOCS --> VALIDATE_DIRS
    DIR_CONFIG --> VALIDATE_DIRS

    %% Configuration File Setup
    VALIDATE_DIRS --> CONFIG_GEN{Configuration<br/>Generation}

    CONFIG_GEN --> PACKAGE_JSON[Generate package.json]
    CONFIG_GEN --> GITIGNORE[Generate .gitignore]
    CONFIG_GEN --> ESLINT[Generate .eslintrc]
    CONFIG_GEN --> PRETTIER[Generate .prettierrc]
    CONFIG_GEN --> CLAUDE_CONFIG[Generate .claude/config]

    %% File Content Generation Pipeline
    PACKAGE_JSON --> CONTENT_PIPE[Content Generation Pipeline]
    GITIGNORE --> CONTENT_PIPE
    ESLINT --> CONTENT_PIPE
    PRETTIER --> CONTENT_PIPE
    CLAUDE_CONFIG --> CONTENT_PIPE

    CONTENT_PIPE --> APPLY_VARS[Apply Template Variables]
    APPLY_VARS --> INJECT_CONFIG[Inject Configuration]
    INJECT_CONFIG --> FORMAT_CODE[Format & Validate]
    FORMAT_CODE --> WRITE_FILES[Write Files to Disk]

    %% Memory & Swarm Setup
    WRITE_FILES --> MEMORY_INIT[Initialize Memory Bank]
    MEMORY_INIT --> SWARM_CONFIG[Configure Swarm Topology]
    SWARM_CONFIG --> HOOKS_SETUP[Setup Integration Hooks]

    %% Final Validation
    HOOKS_SETUP --> FINAL_CHECK{Final Validation}
    FINAL_CHECK -->|Pass| SUCCESS([✅ Initialization Complete])
    FINAL_CHECK -->|Fail| ROLLBACK[Rollback Changes]
    ROLLBACK --> ERROR([❌ Initialization Failed])

    %% Styling
    classDef startEnd fill:#4A90E2,stroke:#2171B5,stroke-width:3px,color:#fff
    classDef decision fill:#F5A623,stroke:#D68910,stroke-width:2px,color:#000
    classDef process fill:#7ED321,stroke:#5BA016,stroke-width:2px,color:#fff
    classDef template fill:#BD10E0,stroke:#9013FE,stroke-width:2px,color:#fff
    classDef config fill:#50E3C2,stroke:#00B6A0,stroke-width:2px,color:#fff
    classDef error fill:#D0021B,stroke:#B71C1C,stroke-width:2px,color:#fff

    class START,SUCCESS startEnd
    class ERROR error
    class CHECK_ENV,TEMPLATE_SELECT,DIR_CHECK,CONFIG_GEN,FINAL_CHECK decision
    class GIT_MODE,NEW_MODE,EXISTING_MODE,CREATE_DIRS,VALIDATE_DIRS,CONTENT_PIPE,APPLY_VARS,INJECT_CONFIG,FORMAT_CODE,WRITE_FILES,MEMORY_INIT,SWARM_CONFIG,HOOKS_SETUP,ROLLBACK process
    class CLAUDE_TEMPLATE,MCP_TEMPLATE,FULL_TEMPLATE,CUSTOM_TEMPLATE,GEN_CLAUDE,GEN_MCP,GEN_FULL,GEN_CUSTOM template
    class DIR_SRC,DIR_TESTS,DIR_DOCS,DIR_CONFIG,PACKAGE_JSON,GITIGNORE,ESLINT,PRETTIER,CLAUDE_CONFIG config
```

## Template Variables & Injection Points

### Dynamic Variables
- `{{PROJECT_NAME}}` - Project directory name
- `{{AUTHOR}}` - Git user name or system user
- `{{DESCRIPTION}}` - Project description from user input
- `{{TIMESTAMP}}` - Current initialization timestamp
- `{{GIT_REMOTE}}` - Remote repository URL if available
- `{{NODE_VERSION}}` - Detected Node.js version
- `{{SPARC_MODE}}` - Selected SPARC methodology mode

### Configuration Injection
- **Package.json**: Dependencies, scripts, metadata
- **Claude Config**: Agent preferences, hooks, memory settings
- **Git Config**: Ignore patterns, hooks, workflow templates
- **Linting Config**: ESLint rules, Prettier formatting
- **Test Config**: Jest/Mocha setup, coverage thresholds