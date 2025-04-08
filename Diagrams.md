<!--markdownlint-disable-->

```mermaid
classDiagram
    class ASTNode {
        <<abstract>>
        +id: string
        +clone()
        +validate()
    }
    
    class BlockNode {
        +children: ASTNode[]
        +addChild(node: ASTNode)
        +removeChild(id: string)
    }
    
    class StatementNode {
        +execute(context: ExecutionContext)
    }
    
    class ArgumentNode {
        <<abstract>>
        +resultType: DataType
        +validate()
    }
    
    class LiteralArgumentNode {
        +value: any
        +evaluate(): any
    }
    
    class ExpressionArgumentNode {
        +operator: string
        +operands: ArgumentNode[]
        +evaluate(context: ExecutionContext): any
    }
    
    class ControlBlockNode {
        +condition: ArgumentNode
        +execute(context: ExecutionContext)
    }
    
    class FunctionBlockNode {
        +parameters: ParameterDefinition[]
        +body: BlockNode
        +execute(context: ExecutionContext, args: any[])
    }
    
    ASTNode <|-- BlockNode
    ASTNode <|-- StatementNode
    ASTNode <|-- ArgumentNode
    
    ArgumentNode <|-- LiteralArgumentNode
    ArgumentNode <|-- ExpressionArgumentNode
    
    BlockNode <|-- ControlBlockNode
    BlockNode <|-- FunctionBlockNode
```

```mermaid
flowchart TD
    start[Program Construction] --> nodeCreation[Create Program Nodes]
    nodeCreation --> connectNodes[Connect Nodes According to Grammar Rules]
    connectNodes --> validation[Validate Structure]
    
    validation --> typeCheck[Type Compatibility Check]
    validation --> structuralCheck[Structural Validation]
    validation --> semanticCheck[Semantic Validation]
    
    typeCheck --> validationResult{Valid?}
    structuralCheck --> validationResult
    semanticCheck --> validationResult
    
    validationResult -- Yes --> readyToExecute[Ready for Execution]
    validationResult -- No --> errorReport[Generate Validation Errors]
    errorReport --> resolveErrors[Resolve Construction Errors]
    resolveErrors --> connectNodes
```

```mermaid
graph TD
    subgraph "Execution State Management"
        direction TB
        global[Global Scope] --> func1[Function A Scope]
        global --> func2[Function B Scope]
        func1 --> nestedFunc[Nested Function Scope]
        func1 --> loop[Loop Block Scope]
        
        global -->|x = 10| globalVar[Global Variable x]
        func1 -->|y = 5| func1Var[Function Variable y]
        func2 -->|x = 20| func2Var[Shadowed Variable x]
        loop -->|i = 0...n| loopVar[Loop Variable i]
        nestedFunc -->|z = 15| nestedVar[Nested Variable z]
        
        event[Variable Change] --> transaction[Transaction Manager]
        transaction --> snapshot[State Snapshot]
        transaction --> update[Atomic Update]
        transaction --> rollback[Rollback Capability]
    end
```

```mermaid
graph TD
    subgraph "Program Engine Framework"
        AST["AST: Program representation model"]
        Parser["Parser: Applies grammar rules to AST"]
        StateManager["State Manager: Execution context & variables"]
        Interpreter["Interpreter: Executes nodes according to operation rules"]
        Timeline["Timeline Manager: Controls time-based execution"]
        ConcurrencyManager["Concurrency Manager: Coordinates execution paths"]
    end

    ExternalTree["External Program Tree"] -->|"Imported via"| AST
    OutputSystems["Output Systems<br>(Audio, Visual, etc)"] <-->|"Register/Invoke"| Interpreter

    Parser -->|"validates"| AST
    Interpreter -->|"uses"| Parser
    Interpreter -->|"maintains"| StateManager
    Interpreter -->|"coordinates with"| Timeline
    ConcurrencyManager -->|"schedules"| Interpreter
    ConcurrencyManager -->|"synchronizes"| StateManager

    classDef component fill:#f9f,stroke:#333,stroke-width:2px;
    classDef external fill:#bbf,stroke:#33f,stroke-width:1px;
    
    class AST,Parser,StateManager,Interpreter,Timeline,ConcurrencyManager component;
    class ExternalTree,OutputSystems external;
```

```mermaid
sequenceDiagram
    participant AST as AST Tree
    participant Parser as Parser
    participant Interpreter as Interpreter
    participant Context as ExecutionContext
    participant State as StateManager
    
    Note over AST,State: Execution Start
    
    Interpreter->>Parser: parseNode(rootNode)
    Parser->>AST: validateNode(rootNode)
    AST-->>Parser: validationResult
    
    alt validation successful
        Parser-->>Interpreter: validatedNode
        Interpreter->>Context: prepareExecution(node)
        Context->>State: createScope()
        State-->>Context: newScope
        
        loop for each child node
            Interpreter->>Parser: parseNode(childNode)
            Parser-->>Interpreter: parsedChild
            Interpreter->>Interpreter: executeNode(parsedChild)
            Interpreter->>State: updateState(result)
        end
        
        Interpreter->>Context: finalizeExecution()
    else validation failed
        Parser-->>Interpreter: validationError
        Interpreter->>Context: handleError(validationError)
    end
```

```mermaid
   classDiagram
    class ExecutionContext {
        -variableScopes: Stack~Scope~
        -globalScope: Scope
        -timeline: Timeline
        +createChildContext()
        +getVariable(name: string): any
        +setVariable(name: string, value: any)
        +getCurrentTime(): number
        +getCurrentScope(): Scope
    }
    
    class Scope {
        -variables: Map~string, any~
        -parent: Scope
        +getVariable(name: string): any
        +setVariable(name: string, value: any)
        +hasVariable(name: string): boolean
    }
    
    class Timeline {
        -currentTime: number
        -timeScale: number
        -scheduledEvents: PriorityQueue~Event~
        +scheduleEvent(time: number, action: Function)
        +advanceTime(duration: number)
        +getCurrentTime(): number
    }
    
    class ExecutionManager {
        -activeContexts: Map~string, ExecutionContext~
        -concurrencyManager: ConcurrencyManager
        +createExecutionContext(): ExecutionContext
        +executeProgram(ast: AST)
        +pauseExecution()
        +resumeExecution()
    }
    
    ExecutionContext --> Scope
    ExecutionContext --> Timeline
    ExecutionManager --> ExecutionContext
    ExecutionManager --> ConcurrencyManager
    Scope --> Scope : parent
```
```mermaid
flowchart TD
    subgraph ConcurrencyManagement
        direction TB
        execPaths[Execution Paths] --> scheduler[Execution Scheduler]
        scheduler --> activeContexts[Active Execution Contexts]
        
        activeContexts --> sync[Synchronization Points]
        sync --> resourceManager[Shared Resource Manager]
        
        resourceManager --> locks[Resource Locks]
        resourceManager --> waitQueues[Wait Queues]
        
        waitQueues --> scheduler
    end
    
    subgraph ProcedureExecution
        direction TB
        mainProc[Main Procedure] --> nestedProc1[Nested Procedure 1]
        mainProc --> nestedProc2[Nested Procedure 2]
        nestedProc1 --> deepNested[Further Nested Procedure]
        
        mainProc --> concurrencyManager[Concurrency Manager]
        nestedProc1 --> concurrencyManager
        nestedProc2 --> concurrencyManager
    end
    
    ProcedureExecution --> ConcurrencyManagement
```

```mermaid
gantt
    title Timeline-Based Execution
    dateFormat  s
    axisFormat %S
    
    section Execution Path 1
    Block 1 Execution  :a1, 0, 2s
    Block 2 Execution  :a2, after a1, 3s
    
    section Execution Path 2
    Block 3 Execution  :b1, 1, 3s
    Block 4 Execution  :b2, after b1, 2s
    
    section Synchronization
    Synchronization Point  :milestone, m1, 4, 0d
    Block 5 Execution  :c1, after m1, 2s
    
    section Timeline Controls
    Pause  :p1, 2, 1s
    Resume :after p1, 1s
```

```mermaid
flowchart TD
    subgraph ErrorHandling
        detection[Error Detection] --> classification[Error Classification]
        classification --> handling[Error Handling Strategy]
        
        classification --> structural[Structural Errors]
        classification --> runtime[Runtime Errors]
        classification --> type[Type Errors]
        classification --> timing[Timing Errors]
        
        structural --> validationErr[Validation Error Service]
        runtime --> executionErr[Execution Error Service]
        type --> typeErr[Type Error Service]
        timing --> timeErr[Timing Error Service]
        
        validationErr --> reporter[Error Reporter]
        executionErr --> reporter
        typeErr --> reporter
        timeErr --> reporter
        
        reporter --> recovery[Recovery Strategy]
        reporter --> notification[User Notification]
        
        recovery --> continue[Continue Execution]
        recovery --> partial[Partial Execution]
        recovery --> terminate[Terminate Execution]
        recovery --> retry[Retry Operation]
    end
```

```mermaid
flowchart TD
    subgraph Serialization
        node[ASTNode] --> toJSON("node.toJSON()")
        toJSON --> jsonObj[JSON Object]
        jsonObj --> storage[Storage/File]
        
        storage --> jsonData[JSON Data]
        jsonData --> ASTSerializer("ASTSerializer.fromJSON")
        ASTSerializer --> factory[NodeFactory]
        factory --> reconstructed[Reconstructed AST]
    end
    
    subgraph Metadata
        NodeMetadata[NodeMetadata Interface] --> id[id: string]
        NodeMetadata --> position["position?: {x,y}"]
        NodeMetadata --> comments["comments?: string[]"]
        NodeMetadata --> colorOverride["colorOverride?: string"]
        
        node2[ASTNode] --> metadata["_metadata: NodeMetadata"]
        metadata --> serialization[Included in serialization]
    end
```

```mermaid
flowchart TD
    subgraph AST
        ast[AST Class] --> rootNodes("rootNodes: ASTNode[]")
        ast --> nodesById("nodesById: Map<string,ASTNode>")
        
        manipulate[AST Manipulation] --> addRoot[addRootNode]
        manipulate --> removeRoot[removeRootNode]
        manipulate --> getById[getNodeById]
        
        addRoot --> register[registerNodeRecursive]
        removeRoot --> unregister[unregisterNodeRecursive]
        
        register --> idMap[Add to nodesById map]
        register --> scanChildren[Scan children]
        register --> scanParams[Scan parameters]
        
        unregister --> idMapRemove[Remove from nodesById map]
        unregister --> scanChildrenRemove[Scan children]
        unregister --> scanParamsRemove[Scan parameters]
    end
```

```mermaid
flowchart TD
    subgraph References
        refMgr[ReferenceManager] --> ast[AST]
        refMgr --> funcDefs("functionDefs: Map<string,BlockNode>")
        refMgr --> funcCalls("functionCalls: Map<string,StatementNode[]>")
        
        build[buildReferenceIndex] --> scan[Scan all AST nodes]
        scan --> identifyDefs[Identify function definitions]
        scan --> identifyCalls[Identify function calls]
        
        identifyDefs --> mapDefs[Map function names to definition nodes]
        identifyCalls --> mapCalls[Map function names to call sites]
        
        validate[validateReferences] --> checkCalls[Check all calls have definitions]
        validate --> reportErrors[Report missing function errors]
    end
```

```mermaid
flowchart TD
    start[Start Parsing] --> findStartBlocks[Find Start Blocks]
    findStartBlocks --> loopStartBlocks[For Each Start Block]
    loopStartBlocks --> createStartNode[Create Start Node]
    createStartNode --> parseContents[Parse Block Contents]
    parseContents --> addToAST[Add to AST]
    
    parseContents --> loopChildren[For Each Child Block]
    loopChildren --> checkType[Check Block Type]
    checkType -- Block --> createBlockNode[Create Block Node]
    createBlockNode --> parseBlockChildren[Parse Block Children]
    checkType -- Statement --> createStatementNode[Create Statement Node]
    createStatementNode --> parseParameters[Parse Parameters]
    
    parseParameters --> loopParams[For Each Parameter]
    loopParams --> parseArgument[Parse Argument]
    parseArgument -- Literal --> createDataNode[Create Data Node]
    parseArgument -- Expression --> createExpressionNode[Create Expression Node]
    
    parseBlockChildren --> loopChildren
    addToAST --> done[Parsing Complete]
```

```mermaid
classDiagram
    class ExecutionContext {
        -variables: Map<string, any>
        -parent: ExecutionContext
        -globalContext: ExecutionContext
        +setVariable(name, value, scope)
        +getVariable(name)
        +createChildContext()
    }
    
    ExecutionContext --> ExecutionContext : parent
    ExecutionContext --> ExecutionContext : globalContext
    
    note for ExecutionContext "Variables are looked up in this context first,then in parent contexts if not found"
```

```mermaid
flowchart TD
    start[Start Execution] --> findEntryPoints[Find Entry Points]
    findEntryPoints --> createContext[Create Global Context]
    createContext --> executeThreads[Execute Each Thread]
    
    subgraph "Node Execution"
        executeNode[Execute Node] --> checkBreakpoint[Check for Breakpoint]
        checkBreakpoint -- Has Breakpoint --> pauseDebugger[Pause for Debugger]
        pauseDebugger --> resumeExecution[Resume After User Action]
        checkBreakpoint -- No Breakpoint --> nodeTypeCheck[Check Node Type]
        resumeExecution --> nodeTypeCheck
        
        nodeTypeCheck -- Block --> executeBlock[Execute Block]
        nodeTypeCheck -- Statement --> executeStatement[Execute Statement]
        nodeTypeCheck -- Argument --> evaluateArgument[Evaluate Argument]
        
        executeBlock --> blockTypeCheck[Check Block Type]
        blockTypeCheck -- Sequence --> executeSequence[Execute Children in Order]
        blockTypeCheck -- Loop --> executeLoop[Execute Children Multiple Times]
        blockTypeCheck -- Conditional --> evaluateCondition[Evaluate Condition]
        evaluateCondition -- True --> executeTrueBranch[Execute True Branch]
        evaluateCondition -- False --> executeFalseBranch[Execute False Branch]
        
        executeStatement --> evaluateParams[Evaluate Parameters]
        evaluateParams --> statementTypeCheck[Check Statement Type]
        statementTypeCheck -- PlayNote --> scheduleNote[Schedule Note]
        statementTypeCheck -- Rest --> scheduleRest[Schedule Rest]
        statementTypeCheck -- Assignment --> assignVariable[Set Variable]
    end
    
    executeThreads --> waitForCompletion[Wait for All Threads]
    waitForCompletion --> done[Execution Complete]
```

```mermaid
flowchart TD
    subgraph "Thread Management"
        spawnThread[Spawn Thread] --> createThread[Create Thread Entry]
        createThread --> executeFunction[Execute Thread Function]
        executeFunction -- Success --> markComplete[Mark Thread Complete]
        executeFunction -- Error --> markError[Mark Thread Error]
        
        waitForAll[Wait for All Threads] --> checkStatus[Check Thread Status]
        checkStatus -- All Complete --> collectResults[Collect Results]
        checkStatus -- Some Running --> waitMore[Wait More]
        waitMore --> checkStatus
    end
    
    subgraph "Time Scheduling"
        scheduleNote[Schedule Note] --> calculateTiming[Calculate Note Timing]
        calculateTiming --> createOscillator[Create Audio Oscillator]
        createOscillator --> createGain[Create Gain Node]
        createGain --> connectNodes[Connect Audio Nodes]
        connectNodes --> scheduleStart[Schedule Start Time]
        scheduleStart --> scheduleStop[Schedule Stop Time]
        scheduleStop --> addToEvents[Add to Scheduled Events]
        addToEvents --> advanceTime[Advance Time Offset]
    end
    
    subgraph "Synchronization"
        thread1[Thread 1] --> access[Access Shared Resource]
        thread2[Thread 2] --> access
        access --> checkLock[Check Resource Lock]
        checkLock -- Locked --> waitUnlock[Wait for Unlock]
        waitUnlock --> checkLock
        checkLock -- Unlocked --> acquireLock[Acquire Lock]
        acquireLock --> useResource[Use Resource]
        useResource --> releaseLock[Release Lock]
    end
```

```mermaid
stateDiagram-v2
    [*] --> Inactive: Create debugger
    
    Inactive --> Active: enable()
    Active --> Inactive: disable()
    
    Active --> Paused: Hit breakpoint
    Paused --> Active: continue()
    
    Paused --> StepOver: stepOver()
    StepOver --> Paused: Pause at next statement
    
    Paused --> StepInto: stepInto()
    StepInto --> Paused: Pause in function
    
    Paused --> StepOut: stepOut()
    StepOut --> Paused: Pause after function returns
    
    state Paused {
        [*] --> InspectingVariables
        InspectingVariables --> ModifyingVariables
        ModifyingVariables --> InspectingVariables
        InspectingVariables --> AddingWatches
        AddingWatches --> InspectingVariables
    }
```

### Execution Flow Diagram
```mermaid
flowchart TD
    A([Program Loaded]) --> B[Parser validates and prepares AST]
    B --> C{Validation successful?}
    C -->|No| D[Report Errors to User]
    D --> E([End])
    
    C -->|Yes| F[Initialize State Manager]
    F --> G[Begin Execution]
    
    %% Parallel execution paths
    G --> H1[Main Thread]
    G --> H2[Music Thread]
    
    %% Main Thread path
    H1 --> I1[Process Control Blocks]
    I1 --> J1[Update Variables]
    J1 --> K1[Check Conditions]
    
    %% Music Thread path
    H2 --> I2[Schedule Notes]
    I2 --> J2[Play Sounds]
    J2 --> K2[Maintain Timing]
    
    %% Synchronization point
    K1 --> L[Synchronization Point]
    K2 --> L
    
    %% Decision for loop or exit
    L --> M{Program Complete?}
    M -->|No| G
    M -->|Yes| N[Cleanup Resources]
    N --> O([End])
    
    %% Styling
    classDef process fill:#a8d5ba,stroke:#178344,stroke-width:1px;
    classDef decision fill:#ffd78c,stroke:#d68f00,stroke-width:1px;
    classDef terminal fill:#d3d3d3,stroke:#333,stroke-width:1px,border-radius:10px;
    
    class A,E,O terminal;
    class B,D,F,G,H1,H2,I1,I2,J1,J2,K1,K2,L,N process;
    class C,M decision;
```