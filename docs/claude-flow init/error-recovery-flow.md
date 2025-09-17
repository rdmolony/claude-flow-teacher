# Error Recovery Flow

```mermaid
flowchart TD
    START([Initialization Start]) --> CHECKPOINT1[Create Initial Checkpoint]

    %% Validation System
    CHECKPOINT1 --> ENV_VALID{Environment<br/>Validation}
    ENV_VALID -->|Pass| TEMPLATE_VALID{Template<br/>Validation}
    ENV_VALID -->|Fail| ENV_ERROR[Environment Error]

    TEMPLATE_VALID -->|Pass| CHECKPOINT2[Template Checkpoint]
    TEMPLATE_VALID -->|Fail| TEMPLATE_ERROR[Template Error]

    %% Directory Creation Phase
    CHECKPOINT2 --> DIR_CREATE{Directory<br/>Creation}
    DIR_CREATE -->|Success| CHECKPOINT3[Directory Checkpoint]
    DIR_CREATE -->|Permission Error| PERM_ERROR[Permission Error]
    DIR_CREATE -->|Disk Space Error| DISK_ERROR[Disk Space Error]
    DIR_CREATE -->|Path Error| PATH_ERROR[Path Error]

    %% File Generation Phase
    CHECKPOINT3 --> FILE_GEN{File Generation}
    FILE_GEN -->|Success| CHECKPOINT4[File Generation Checkpoint]
    FILE_GEN -->|Write Error| WRITE_ERROR[Write Error]
    FILE_GEN -->|Template Error| TMPL_ERROR[Template Processing Error]

    %% Configuration Phase
    CHECKPOINT4 --> CONFIG_SETUP{Configuration<br/>Setup}
    CONFIG_SETUP -->|Success| CHECKPOINT5[Configuration Checkpoint]
    CONFIG_SETUP -->|Invalid Config| CONFIG_ERROR[Configuration Error]
    CONFIG_SETUP -->|Dependency Error| DEP_ERROR[Dependency Error]

    %% MCP Integration Phase
    CHECKPOINT5 --> MCP_INIT{MCP Server<br/>Initialization}
    MCP_INIT -->|Success| CHECKPOINT6[MCP Checkpoint]
    MCP_INIT -->|Connection Error| MCP_ERROR[MCP Connection Error]
    MCP_INIT -->|Timeout Error| TIMEOUT_ERROR[MCP Timeout Error]

    %% Final Validation
    CHECKPOINT6 --> FINAL_VALID{Final<br/>Validation}
    FINAL_VALID -->|Pass| SUCCESS([✅ Success])
    FINAL_VALID -->|Fail| FINAL_ERROR[Final Validation Error]

    %% Error Recovery Mechanisms
    ENV_ERROR --> ERROR_HANDLER{Error Type<br/>Analysis}
    TEMPLATE_ERROR --> ERROR_HANDLER
    PERM_ERROR --> ERROR_HANDLER
    DISK_ERROR --> ERROR_HANDLER
    PATH_ERROR --> ERROR_HANDLER
    WRITE_ERROR --> ERROR_HANDLER
    TMPL_ERROR --> ERROR_HANDLER
    CONFIG_ERROR --> ERROR_HANDLER
    DEP_ERROR --> ERROR_HANDLER
    MCP_ERROR --> ERROR_HANDLER
    TIMEOUT_ERROR --> ERROR_HANDLER
    FINAL_ERROR --> ERROR_HANDLER

    %% Recovery Strategy Selection
    ERROR_HANDLER -->|Recoverable| RECOVERY_STRATEGY{Recovery<br/>Strategy}
    ERROR_HANDLER -->|Fatal| FATAL_CLEANUP[Fatal Error Cleanup]

    RECOVERY_STRATEGY -->|Retry| RETRY_LOGIC[Retry with Backoff]
    RECOVERY_STRATEGY -->|Rollback| ROLLBACK_LOGIC[Rollback to Checkpoint]
    RECOVERY_STRATEGY -->|Alternative| ALT_STRATEGY[Alternative Strategy]
    RECOVERY_STRATEGY -->|User Input| USER_RESOLVE[Request User Resolution]

    %% Retry Logic
    RETRY_LOGIC --> RETRY_COUNT{Retry Count<br/>< Max?}
    RETRY_COUNT -->|Yes| EXPONENTIAL_BACKOFF[Exponential Backoff]
    RETRY_COUNT -->|No| ROLLBACK_LOGIC

    EXPONENTIAL_BACKOFF --> RETRY_OPERATION[Retry Operation]
    RETRY_OPERATION --> ENV_VALID

    %% Rollback Logic
    ROLLBACK_LOGIC --> FIND_CHECKPOINT[Find Last Valid Checkpoint]
    FIND_CHECKPOINT --> RESTORE_STATE[Restore System State]
    RESTORE_STATE --> CLEANUP_PARTIAL[Cleanup Partial Changes]
    CLEANUP_PARTIAL --> ROLLBACK_COMPLETE[Rollback Complete]

    %% Alternative Strategy
    ALT_STRATEGY --> ALT_TEMPLATE[Try Alternative Template]
    ALT_STRATEGY --> ALT_PATH[Try Alternative Path]
    ALT_STRATEGY --> ALT_CONFIG[Try Alternative Configuration]

    ALT_TEMPLATE --> TEMPLATE_VALID
    ALT_PATH --> DIR_CREATE
    ALT_CONFIG --> CONFIG_SETUP

    %% User Resolution
    USER_RESOLVE --> USER_INPUT[Collect User Input]
    USER_INPUT --> APPLY_RESOLUTION[Apply User Resolution]
    APPLY_RESOLUTION --> ENV_VALID

    %% Final Outcomes
    ROLLBACK_COMPLETE --> PARTIAL_SUCCESS[Partial Success - Manual Fix Required]
    FATAL_CLEANUP --> COMPLETE_FAILURE([❌ Complete Failure])

    %% State Persistence
    subgraph CHECKPOINTS ["Checkpoint System"]
        CP_STORAGE[(Checkpoint Storage)]
        CP_ENV[Environment State]
        CP_FILES[File System State]
        CP_CONFIG[Configuration State]
        CP_MCP[MCP State]
    end

    CHECKPOINT1 --> CP_STORAGE
    CHECKPOINT2 --> CP_STORAGE
    CHECKPOINT3 --> CP_STORAGE
    CHECKPOINT4 --> CP_STORAGE
    CHECKPOINT5 --> CP_STORAGE
    CHECKPOINT6 --> CP_STORAGE

    CP_STORAGE --> CP_ENV
    CP_STORAGE --> CP_FILES
    CP_STORAGE --> CP_CONFIG
    CP_STORAGE --> CP_MCP

    FIND_CHECKPOINT --> CP_STORAGE

    %% Styling
    classDef success fill:#7ED321,stroke:#5BA016,stroke-width:3px,color:#fff
    classDef error fill:#D0021B,stroke:#B71C1C,stroke-width:2px,color:#fff
    classDef warning fill:#F5A623,stroke:#D68910,stroke-width:2px,color:#000
    classDef checkpoint fill:#4A90E2,stroke:#2171B5,stroke-width:2px,color:#fff
    classDef recovery fill:#BD10E0,stroke:#9013FE,stroke-width:2px,color:#fff
    classDef storage fill:#50E3C2,stroke:#00B6A0,stroke-width:2px,color:#fff

    class SUCCESS,ROLLBACK_COMPLETE success
    class ENV_ERROR,TEMPLATE_ERROR,PERM_ERROR,DISK_ERROR,PATH_ERROR,WRITE_ERROR,TMPL_ERROR,CONFIG_ERROR,DEP_ERROR,MCP_ERROR,TIMEOUT_ERROR,FINAL_ERROR,FATAL_CLEANUP,COMPLETE_FAILURE error
    class PARTIAL_SUCCESS warning
    class CHECKPOINT1,CHECKPOINT2,CHECKPOINT3,CHECKPOINT4,CHECKPOINT5,CHECKPOINT6 checkpoint
    class ERROR_HANDLER,RECOVERY_STRATEGY,RETRY_LOGIC,ROLLBACK_LOGIC,ALT_STRATEGY,USER_RESOLVE recovery
    class CP_STORAGE,CP_ENV,CP_FILES,CP_CONFIG,CP_MCP storage
```

## Error Recovery Strategies

### Environment Validation Errors
```javascript
// Node.js Version Mismatch
{
  "error": "UNSUPPORTED_NODE_VERSION",
  "recovery": "prompt_node_upgrade",
  "fallback": "legacy_mode"
}

// Git Repository Issues
{
  "error": "NOT_GIT_REPO",
  "recovery": "git_init",
  "fallback": "standalone_mode"
}

// Permission Issues
{
  "error": "INSUFFICIENT_PERMISSIONS",
  "recovery": "sudo_prompt",
  "fallback": "user_directory"
}
```

### Template Processing Errors
```javascript
// Missing Template Variables
{
  "error": "MISSING_TEMPLATE_VAR",
  "recovery": "prompt_user_input",
  "fallback": "default_values"
}

// Template Syntax Errors
{
  "error": "TEMPLATE_SYNTAX_ERROR",
  "recovery": "alternative_template",
  "fallback": "minimal_template"
}
```

### File System Errors
```javascript
// Disk Space Insufficient
{
  "error": "ENOSPC",
  "recovery": "cleanup_temp_files",
  "fallback": "minimal_installation"
}

// Path Too Long (Windows)
{
  "error": "ENAMETOOLONG",
  "recovery": "shorten_paths",
  "fallback": "root_installation"
}
```

### MCP Server Errors
```javascript
// Connection Timeout
{
  "error": "MCP_TIMEOUT",
  "recovery": "increase_timeout",
  "fallback": "local_mode"
}

// Server Unavailable
{
  "error": "MCP_UNAVAILABLE",
  "recovery": "alternative_server",
  "fallback": "offline_mode"
}
```

## Checkpoint System Details

### Checkpoint Data Structure
```json
{
  "timestamp": "2024-01-15T10:30:00Z",
  "phase": "template_generation",
  "state": {
    "environment": {
      "nodeVersion": "18.17.0",
      "gitRepo": true,
      "workingDirectory": "/project"
    },
    "filesCreated": [
      ".claude/config.json",
      "src/index.js"
    ],
    "configurationsApplied": [
      "package.json",
      ".eslintrc"
    ]
  },
  "rollbackInstructions": [
    "rm -rf .claude/",
    "git checkout HEAD -- package.json"
  ]
}
```

### Recovery Policies
- **Maximum Retries**: 3 attempts with exponential backoff
- **Checkpoint Retention**: Keep last 5 checkpoints
- **Rollback Scope**: Phase-specific or complete rollback
- **User Intervention**: Automatic for non-destructive operations
- **Cleanup Strategy**: Remove partial installations on failure