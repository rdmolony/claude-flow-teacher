# Component Interaction Diagram

```mermaid
graph TB
    %% Main Entry Points
    CLI[Claude Flow CLI<br/>claude-flow init]
    NPX[npx claude-flow@alpha init]

    %% Core Modules
    INIT[init/index.js<br/>Main Handler]
    BATCH[batch-init.js<br/>Parallel Processing]
    HIVE[hive-mind-init.js<br/>Swarm Setup]
    REGISTRY[command-registry.js<br/>Routing]

    %% Template System
    TEMPLATES[Template System]
    CLAUDE_MD[claude-md.js<br/>Template]
    MEMORY_MD[memory-bank-md.js<br/>Template]
    CONFIG_TMPL[Configuration<br/>Templates]

    %% File Operations
    VALIDATOR[Validation System]
    GENERATOR[File Generator]
    DIR_CREATOR[Directory Creator]

    %% External Systems
    GIT[Git Repository]
    MCP[MCP Servers]
    CLAUDE_CODE[Claude Code CLI]

    %% Styling
    classDef primary fill:#4A90E2,stroke:#2171B5,stroke-width:3px,color:#fff
    classDef secondary fill:#7ED321,stroke:#5BA016,stroke-width:2px,color:#fff
    classDef template fill:#F5A623,stroke:#D68910,stroke-width:2px,color:#fff
    classDef external fill:#BD10E0,stroke:#9013FE,stroke-width:2px,color:#fff

    class CLI,NPX,INIT primary
    class BATCH,HIVE,REGISTRY secondary
    class TEMPLATES,CLAUDE_MD,MEMORY_MD,CONFIG_TMPL template
    class GIT,MCP,CLAUDE_CODE external

    %% Flow Connections
    CLI --> INIT
    NPX --> INIT

    INIT --> REGISTRY
    REGISTRY --> BATCH
    REGISTRY --> HIVE

    BATCH --> VALIDATOR
    BATCH --> GENERATOR
    BATCH --> DIR_CREATOR

    HIVE --> TEMPLATES
    TEMPLATES --> CLAUDE_MD
    TEMPLATES --> MEMORY_MD
    TEMPLATES --> CONFIG_TMPL

    GENERATOR --> CLAUDE_MD
    GENERATOR --> MEMORY_MD
    GENERATOR --> CONFIG_TMPL

    VALIDATOR --> GIT
    DIR_CREATOR --> GIT

    INIT --> MCP
    HIVE --> MCP

    BATCH --> CLAUDE_CODE

    %% Labels
    CLI -.->|"Arguments & Options"| INIT
    INIT -.->|"Route Commands"| REGISTRY
    REGISTRY -.->|"Parallel Tasks"| BATCH
    REGISTRY -.->|"Swarm Setup"| HIVE

    BATCH -.->|"Validate Project"| VALIDATOR
    BATCH -.->|"Generate Files"| GENERATOR
    BATCH -.->|"Create Structure"| DIR_CREATOR

    HIVE -.->|"Load Templates"| TEMPLATES
    GENERATOR -.->|"Apply Templates"| CLAUDE_MD

    VALIDATOR -.->|"Check Git Status"| GIT
    INIT -.->|"Configure Servers"| MCP
    BATCH -.->|"Invoke Commands"| CLAUDE_CODE
```

## Component Responsibilities

### Core Components
- **init/index.js**: Main initialization handler and orchestrator
- **batch-init.js**: Handles parallel processing of initialization tasks
- **hive-mind-init.js**: Sets up swarm coordination and agent spawning
- **command-registry.js**: Routes commands to appropriate handlers

### Template System
- **claude-md.js**: Generates Claude Code configuration templates
- **memory-bank-md.js**: Creates memory management templates
- **Configuration Templates**: Various config file templates

### Integration Points
- **Claude Code CLI**: Primary execution environment
- **MCP Servers**: claude-flow, ruv-swarm, flow-nexus coordination
- **Git Repository**: Version control integration