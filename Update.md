<!--markdownlint-disable-->
# Music Blocks 4 Program Engine

## Program Representation Architecture

### AST Design

The Program Engine requires a carefully designed Abstract Syntax Tree structure that separates execution concerns from presentation concerns:

#### Node Class Hierarchy and Instance Attributes

Each AST node type will have specific attributes relevant to its role in program execution:

- **Base `ASTNode`**:
  - `id: string` - Unique identifier for referencing
  - `nodeType: NodeType` - Enum identifying the node's category
  - `metadata: ExecutionMetadata` - Contains execution-specific information (line numbers, source positions for error reporting)
  - `_parent: ASTNode | null` - Reference to parent node
  - Virtual methods: `validate()`, `clone()`, `toJSON()`
   
- **`BlockNode` extends `ASTNode`**:
  - `childNodes: ASTNode[]` - Ordered list of child nodes
  - `socketMap: Map<string, ASTNode>` - Named connections to other nodes
  - `blockType: BlockType` - Specific type (loop, conditional, etc.)
  - `branchTargets: Map<string, ASTNode>` - References to branch targets (for loops/conditionals)
  - Methods: `addChild()`, `removeChild()`, `getSocketNode()`, `setSocketNode()`
   
- **`StatementNode` extends `ASTNode`**:
  - `operationType: OperationType` - Specific operation to perform
  - `parameters: Map<string, ArgumentNode>` - Named parameters for the operation
  - `returnType: ValueType | null` - Expected return type, if any
  - Methods: `setParameter()`, `getParameter()`, `validateParameters()`
   
- **`ArgumentNode` extends `ASTNode`**:
  - `dataType: ValueType` - Type of data this argument represents
  - `isLiteral: boolean` - Whether node is a literal value or expression
  - `referenceTarget: string | null` - For variable references, the variable name
  - Methods: `evaluate()`, `isCompatibleWith(targetType: ValueType)`

#### AST vs Program Tree Separation

The AST will be explicitly separated from the UI program tree:
   
- AST contains only execution-relevant information
- UI concerns (workspace coordinates, block colors, visual connections) are stored separately
- Translation layer maintains bidirectional mapping between AST nodes and visual blocks
- When visual blocks are manipulated, corresponding AST changes are validated before being applied

#### Node Connectivity and Validation

Connectivity between nodes will be managed through:
   
- Type validation system that verifies compatible connections
- Socket interface definitions specifying allowed connections
- Pre-connection validation hooks that run before nodes are connected
- Post-connection validation to ensure structural integrity
   
Example validation rules:
- Value type compatibility (number cannot connect to boolean socket)
- Structural constraints (loop body must be a block)
- Semantic constraints (variable must be defined before use)

#### Change Monitoring

AST modifications will be monitored through:
   
- Observer pattern implementation with before/after change events
- Transaction-based changes allowing atomic operations and rollback
- Change record maintaining history for undo/redo operations
- Validation hooks running on each change to maintain AST integrity

## Technical Interfaces

### Framework Neutrality and Extension Points

The Program Engine will be designed as a framework-neutral system with clear extension points:

1. **Instruction Execution Strategy**:
   - Operation executors are registered through a factory system
   - Custom operations can be registered without modifying core engine
   - Operation implementations are decoupled from operation definitions
   
2. **Execution Context Providers**:
   - External systems (audio, graphics, I/O) expose capabilities through context providers
   - Engine accesses these capabilities through abstract interfaces
   - Example: Audio operations simply call methods on IAudioContext without knowing implementation details

3. **Node Type Extension System**:
   - New AST node types can be registered at runtime
   - Node type definitions include validation rules and execution behaviors
   - Custom serialization/deserialization can be provided for each node type

### Event-Based Communication

Communication between the Program Engine and external systems will use:

- Event emitter pattern for asynchronous notifications
- Structured message formats for data exchange
- Subscription model allowing targeted event reception

Key event categories:
- Execution lifecycle events (start, pause, resume, stop)
- State change events (variable updates, context changes)
- Error and warning events (runtime errors, validation issues)
- Resource request events (audio playback, graphics rendering)

### State Access Interfaces

Program state will be accessible through:

- Read-only snapshots of current execution context
- Transaction-based write operations
- Query API for examining variables and execution state
- Observation hooks for state changes

## State Manager

The state manager will provide sophisticated variable management and execution state tracking:

### Variable Management Architecture

- **Multi-Scoped Variable System**:
  - Hierarchical scope chain (global, function, block, loop iteration)
  - Each scope implemented as immutable record for consistency
  - Variable lookup follows scope chain from innermost to outermost
  - Shadow variables in inner scopes can mask outer scope variables

- **Explicit vs Implicit Variable Declaration**:
  - Explicit declaration through variable creation blocks
  - Implicit declaration when writing to undefined variables (configurable behavior)
  - Type inference for implicitly declared variables
  - Runtime type checking with configurable strictness levels

- **Variable Lifecycle Management**:
  - Creation events dispatched when variables enter scope
  - Update events for value changes
  - Disposal events when variables go out of scope
  - Reference counting for complex values (e.g., lists, objects)

### Execution Context Implementation

- **Context Frames**:
  - Stack of execution frames representing current call hierarchy
  - Each frame contains local scope variables and execution state
  - Return value slot for capturing function results
  - Exception slot for error propagation

- **Context Snapshots and Restoration**:
  - Support for capturing execution state at any point
  - Ability to restore execution from saved snapshots
  - Differential snapshots for efficient state saving
  - Serializable context for persistent storage

- **Execution Tracking**:
  - Current instruction pointer for each active thread
  - Call stack representing nested function calls
  - Execution history for debugging and time-travel
  - Performance profiling data collection

### Thread-Local vs Shared State

- **Thread-Local Variables**:
  - Each execution thread maintains independent local variables
  - Thread-local contexts prevent race conditions
  - Copy-on-write semantics for derived variables

- **Shared State Management**:
  - Global variables accessible across all threads
  - Atomic operations for concurrent access to shared variables
  - Mutex mechanisms for critical sections
  - Transactional updates for composite operations

### State Observability

- **Variable Watching**:
  - Register watches on specific variables or patterns
  - Immediate notification of value changes
  - Expression watches for computed values
  - Conditional watches triggered only when condition is met

- **Execution Observations**:
  - Block entry/exit events
  - Thread lifecycle events
  - Synchronization point notifications
  - Error and exception events

## Functional Specifications

### Core Programming Capabilities

#### Control Flow

The Program Engine will support sophisticated control flow mechanisms:

- **Conditional Structures**:
  - Basic if/else with boolean condition
  - Multi-branch conditionals (if/else if/else)
  - Switch/case for multi-way branching
  - Conditional expression evaluation (ternary operations)

- **Loop Structures**:
  - Count-controlled loops (repeat n times)
  - Collection iteration loops (for each)
  - Condition-controlled loops (while, until)
  - Infinite loops with explicit break conditions
  - Loop control statements (break, continue, exit)

- **Subroutines and Procedures**:
  - Named action blocks with parameter passing
  - Return value handling
  - Recursive calls with stack management
  - Optional and default parameters
  - Higher-order functions (functions as arguments)

- **Exception Handling**:
  - Try/catch mechanisms for error recovery
  - Custom exception types
  - Finally blocks for cleanup operations
  - Exception bubbling through call stack

#### Object-Oriented Patterns

The engine will provide foundations for object-oriented programming:

- **Object Representation**:
  - Property-based objects with key-value storage
  - Method attachment to objects
  - Property access and mutation operations

- **Prototype-based Inheritance**:
  - Object cloning and extension
  - Prototype chain for property resolution
  - Method overriding and super calls

- **Message Passing**:
  - Method invocation on objects
  - Dynamic dispatch based on object type
  - Method delegation to prototypes

#### Event-Driven Programming

Enhanced event handling capabilities:

- **Event Definition and Registration**:
  - Define custom events with parameters
  - Register event handlers with priorities
  - Conditional event registration

- **Event Broadcasting**:
  - Trigger events from any execution context
  - Broadcast to specific targets or globally
  - Bubble events through containment hierarchy

- **Event-Based Execution Model**:
  - Start blocks triggered by events
  - Event-based program initialization
  - Wait-for-event blocks for synchronization
  - Event listener lifetime management