# Claude Flow Init Command Execution Flow

This document provides detailed Mermaid diagrams showing the claude-flow init command execution flow, including all major paths, error handling, and rollback mechanisms.

## 1. Main Execution Flow

```mermaid
flowchart TD
    A[User runs: npx claude-flow init] --> B[Command Registry routes to initCommand]
    B --> C{Parse Flags & Determine Mode}
    C -->|--help| D[Show Help & Exit]
    C -->|--flow-nexus| E[Flow Nexus Minimal Init]
    C -->|No special flags| F[Enhanced Claude Flow v2 Init]
    C -->|--basic/--minimal/--sparc| G[Standard Init Mode]
    C -->|--verify/--pair| H[Verification Mode]

    F --> I[Check Existing Files]
    G --> I
    H --> I
    E --> J[Create Flow Nexus Files Only]

    I --> K{Files Exist & !--force?}
    K -->|Yes| L[Show Warning & Exit]
    K -->|No| M[Start Initialization Process]

    M --> N[Create Core Files]
    N --> O[Setup Directory Structure]
    O --> P[Initialize Memory System]
    P --> Q[Setup Coordination System]
    Q --> R[Create Local Executable]
    R --> S{--roo flag?}
    S -->|Yes| T[Initialize SPARC Environment]
    S -->|No| U[Setup Hive-Mind System]
    T --> U
    U --> V[Update .gitignore]
    V --> W{Claude Code Installed?}
    W -->|Yes| X[Setup MCP Servers]
    W -->|No| Y[Show Installation Instructions]
    X --> Z[Success Message]
    Y --> Z
    Z --> AA[End]

    style A fill:#e1f5fe
    style AA fill:#c8e6c9
    style L fill:#ffcdd2
    style D fill:#fff3e0
```

## 2. Template Generation Sequence

```mermaid
sequenceDiagram
    participant User
    participant InitCommand
    participant TemplateSystem
    participant FileSystem
    participant ValidationSystem
    participant HiveMind
    participant SPARC

    User->>InitCommand: npx claude-flow init [flags]
    InitCommand->>InitCommand: Parse flags & determine mode

    alt Enhanced Claude Flow v2
        InitCommand->>TemplateSystem: Create optimized templates
        TemplateSystem->>FileSystem: Generate CLAUDE.md
        TemplateSystem->>FileSystem: Create settings.json
        TemplateSystem->>FileSystem: Generate .mcp.json
        TemplateSystem->>FileSystem: Create config files
    else Standard Init
        InitCommand->>TemplateSystem: Use template copier
        TemplateSystem->>TemplateSystem: Validate templates exist
        alt Templates Available
            TemplateSystem->>FileSystem: Copy revised templates
        else Fallback
            TemplateSystem->>FileSystem: Generate default templates
        end
    end

    InitCommand->>FileSystem: Create directory structure
    FileSystem-->>InitCommand: Directories created

    InitCommand->>HiveMind: Initialize hive-mind system
    HiveMind->>FileSystem: Create .hive-mind directory
    HiveMind->>FileSystem: Initialize database (SQLite or JSON)
    HiveMind->>FileSystem: Create configurations
    HiveMind-->>InitCommand: Hive-mind ready

    opt SPARC Mode (--roo flag)
        InitCommand->>SPARC: Initialize SPARC environment
        SPARC->>FileSystem: Run create-sparc
        SPARC->>FileSystem: Create slash commands
        SPARC-->>InitCommand: SPARC ready
    end

    InitCommand->>ValidationSystem: Validate setup
    ValidationSystem-->>InitCommand: Validation results

    InitCommand->>User: Success message with next steps
```

## 3. State Transitions During Initialization

```mermaid
stateDiagram-v2
    [*] --> Parsing: Command received
    Parsing --> ModeSelection: Flags parsed

    ModeSelection --> FlowNexusInit: --flow-nexus
    ModeSelection --> EnhancedInit: Default/Enhanced
    ModeSelection --> StandardInit: --basic/--minimal/--sparc
    ModeSelection --> VerificationInit: --verify/--pair
    ModeSelection --> HelpDisplay: --help

    HelpDisplay --> [*]: Exit

    FlowNexusInit --> CreatingFiles: Minimal setup
    EnhancedInit --> ConflictCheck: Check existing files
    StandardInit --> ConflictCheck
    VerificationInit --> ConflictCheck

    ConflictCheck --> FileConflict: Files exist & !force
    ConflictCheck --> CoreCreation: No conflicts
    FileConflict --> [*]: Exit with warning

    CoreCreation --> DirectorySetup: Core files created
    DirectorySetup --> MemoryInit: Directories ready
    MemoryInit --> CoordinationSetup: Memory system ready
    CoordinationSetup --> ExecutableCreation: Coordination ready
    ExecutableCreation --> SPARCCheck: Local executable created

    SPARCCheck --> SPARCInit: --roo flag present
    SPARCCheck --> HiveMindInit: No SPARC needed
    SPARCInit --> HiveMindInit: SPARC environment ready

    HiveMindInit --> MCPSetup: Hive-mind initialized
    MCPSetup --> GitIgnoreUpdate: MCP servers configured
    GitIgnoreUpdate --> ValidationPhase: Git ignore updated
    ValidationPhase --> Success: All validations passed
    ValidationPhase --> PartialSuccess: Some warnings

    Success --> [*]: Complete
    PartialSuccess --> [*]: Complete with warnings

    CreatingFiles --> Success: Flow Nexus files created

    state ConflictCheck {
        [*] --> CheckingCLAUDE_md
        CheckingCLAUDE_md --> Checkingsettings_json
        Checkingsettings_json --> Checking_mcp_json
        Checking_mcp_json --> CheckingConfig
        CheckingConfig --> [*]
    }

    state HiveMindInit {
        [*] --> CreatingDirs: Create .hive-mind structure
        CreatingDirs --> InitDB: Initialize database
        InitDB --> CreateConfigs: Setup configurations
        CreateConfigs --> CreateDocs: Generate documentation
        CreateDocs --> [*]: Hive-mind ready

        InitDB --> SQLiteDB: better-sqlite3 available
        InitDB --> JSONFallback: SQLite not available
        SQLiteDB --> CreateConfigs
        JSONFallback --> CreateConfigs
    }
```

## 4. Data Flow from Input to Output Files

```mermaid
graph LR
    A[CLI Args & Flags] --> B[Flag Parser]
    B --> C[Mode Determiner]

    C --> D{Init Mode}
    D -->|enhanced| E[Enhanced Templates]
    D -->|standard| F[Standard Templates]
    D -->|flow-nexus| G[Flow Nexus Templates]
    D -->|verification| H[Verification Templates]

    E --> I[Template Generator]
    F --> J[Template Copier]
    G --> K[Minimal Template Creator]
    H --> L[Verification Template Creator]

    I --> M[File System Operations]
    J --> M
    K --> M
    L --> M

    M --> N[Core Files]
    M --> O[Directory Structure]
    M --> P[Configuration Files]
    M --> Q[Executable Scripts]

    N --> N1[CLAUDE.md]
    N --> N2[memory-bank.md]
    N --> N3[coordination.md]

    O --> O1[memory/]
    O --> O2[coordination/]
    O --> O3[.claude/]
    O --> O4[.hive-mind/]
    O --> O5[.swarm/]

    P --> P1[.claude/settings.json]
    P --> P2[.mcp.json]
    P --> P3[claude-flow.config.json]
    P --> P4[.hive-mind/config.json]

    Q --> Q1[claude-flow]
    Q --> Q2[claude-flow.bat]
    Q --> Q3[claude-flow.ps1]

    R[SPARC Integration] --> S[create-sparc]
    S --> T[.roomodes]
    S --> U[Claude Slash Commands]

    V[Hive-Mind System] --> W[Database Init]
    W --> W1[hive.db / memory.json]
    W --> W2[Performance Indexes]

    V --> X[Default Configs]
    X --> X1[queens.json]
    X --> X2[workers.json]

    style A fill:#e3f2fd
    style N1 fill:#c8e6c9
    style N2 fill:#c8e6c9
    style N3 fill:#c8e6c9
    style W1 fill:#fff9c4
    style T fill:#f3e5f5
```

## 5. Error Handling and Rollback Mechanisms

```mermaid
flowchart TD
    A[Initialization Process] --> B[Enhanced Init with Rollback?]
    B -->|Yes| C[Initialize Rollback System]
    B -->|No| D[Standard Error Handling]

    C --> E[Create Pre-Init Backup]
    E --> F{Backup Success?}
    F -->|No| G[Log Error & Continue]
    F -->|Yes| H[Begin Atomic Operation]

    H --> I[Phase 1: Create Files]
    I --> J[Create Checkpoint]
    J --> K{Phase Success?}
    K -->|No| L[Rollback to Checkpoint]
    K -->|Yes| M[Phase 2: Directory Structure]

    M --> N[Create Checkpoint]
    N --> O{Phase Success?}
    O -->|No| P[Rollback to Previous Checkpoint]
    O -->|Yes| Q[Phase 3: Memory System]

    Q --> R[Continue with Checkpoints...]
    R --> S[Final Validation]
    S --> T{Validation Success?}
    T -->|No| U[Full Rollback]
    T -->|Yes| V[Commit Changes]

    D --> W[Try-Catch Blocks]
    W --> X{Error Occurs?}
    X -->|Yes| Y[Log Error Message]
    X -->|No| Z[Continue Process]
    Y --> AA[Partial Cleanup]
    AA --> BB[Exit with Error Code]

    L --> CC[Update State Tracker]
    P --> CC
    U --> DD[Restore from Backup]
    DD --> EE[Clean Up Temp Files]

    V --> FF[Success]
    CC --> GG[Recovery Attempted]
    EE --> HH[Rollback Complete]

    style A fill:#e1f5fe
    style FF fill:#c8e6c9
    style BB fill:#ffcdd2
    style HH fill:#fff3e0
    style GG fill:#f3e5f5
```

## 6. Validation System Flow

```mermaid
graph TB
    A[Validation Request] --> B{Validation Type}
    B -->|pre-init| C[Pre-Init Validator]
    B -->|post-init| D[Post-Init Validator]
    B -->|config| E[Config Validator]
    B -->|health| F[Health Checker]

    C --> C1[Check Permissions]
    C --> C2[Check Disk Space]
    C --> C3[Check Conflicts]
    C --> C4[Check Dependencies]
    C --> C5[Check Environment]

    D --> D1[Check File Integrity]
    D --> D2[Check Completeness]
    D --> D3[Validate Structure]
    D --> D4[Check File Permissions]

    E --> E1[Validate .roomodes]
    E --> E2[Validate CLAUDE.md]
    E --> E3[Validate Memory Config]
    E --> E4[Validate Coordination]

    F --> F1[Check Mode Availability]
    F --> F2[Check Template Integrity]
    F --> F3[Check Config Consistency]
    F --> F4[Check System Resources]

    C1 --> G[Aggregate Results]
    C2 --> G
    C3 --> G
    C4 --> G
    C5 --> G

    D1 --> H[Aggregate Results]
    D2 --> H
    D3 --> H
    D4 --> H

    E1 --> I[Aggregate Results]
    E2 --> I
    E3 --> I
    E4 --> I

    F1 --> J[Aggregate Results]
    F2 --> J
    F3 --> J
    F4 --> J

    G --> K[Generate Report]
    H --> K
    I --> K
    J --> K

    K --> L[Return Results]

    style A fill:#e3f2fd
    style L fill:#c8e6c9
    style K fill:#fff9c4
```

## Key Components and Responsibilities

### 1. Command Registry
- Routes `init` command to `initCommand` handler
- Manages command metadata and help information
- Provides extensible command registration system

### 2. Init Command Handler
- Parses command line flags and arguments
- Determines initialization mode (enhanced, standard, flow-nexus, verification)
- Orchestrates the entire initialization process
- Handles different execution paths based on flags

### 3. Template System
- **Enhanced Templates**: Generated programmatically for v2.0.0 features
- **Standard Templates**: File-based templates copied from repository
- **Flow Nexus**: Minimal setup with only essential files
- **Verification**: Special templates for verification and pair programming

### 4. Rollback System
- **Backup Manager**: Creates and manages backups before initialization
- **State Tracker**: Tracks initialization phases and checkpoints
- **Rollback Executor**: Performs full or partial rollbacks
- **Recovery Manager**: Handles automatic recovery from common failures

### 5. Validation System
- **Pre-Init Validation**: Checks permissions, disk space, conflicts
- **Post-Init Validation**: Verifies file integrity and completeness
- **Config Validation**: Validates configuration files and structure
- **Health Checker**: Performs system health and resource checks

### 6. Hive-Mind Initialization
- Creates comprehensive directory structure (`.hive-mind/`)
- Initializes database (SQLite with fallback to JSON)
- Sets up queen and worker configurations
- Creates performance monitoring and consensus systems

### 7. SPARC Integration
- Only activated with `--roo` flag
- Runs `create-sparc` for full SPARC environment
- Creates Claude Code slash commands
- Sets up specialized development modes

### 8. MCP Server Setup
- Automatically detects Claude Code installation
- Registers claude-flow, ruv-swarm, and flow-nexus MCP servers
- Creates project-level `.mcp.json` configuration
- Provides manual setup instructions if Claude Code not installed

## Error Handling Strategies

1. **Graceful Degradation**: Falls back to simpler alternatives when advanced features fail
2. **Atomic Operations**: Uses rollback system for enhanced initialization
3. **Comprehensive Logging**: Provides detailed error messages and context
4. **Recovery Mechanisms**: Automatic cleanup and recovery from partial failures
5. **Validation Gates**: Multiple validation phases prevent invalid states

This documentation provides a comprehensive view of the claude-flow init command execution flow, showing all major decision points, error handling paths, and system integrations.